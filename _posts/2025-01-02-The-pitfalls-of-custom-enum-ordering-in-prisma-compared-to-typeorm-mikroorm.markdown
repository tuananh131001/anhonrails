---
layout: single
title:  "The Pitfalls of Custom Enum Ordering in Prisma Compared to TypeORM & MikroORM"
date:   2025-02-01 00:00:00 +0700
categories: prisma, orm, typescript, typeorm, mikroorm
---

"Who use Prisma in production?" This is statement is true in my daily work. During my work on a project with Prisma as ORM, whenever I worked on a feature, I usually get alots of open issue which block my development and get things done.

I come up with this article base on my curiosity on how other ORM can handle the mapping of fields and custom enum ordering equally well. We’ll walk through the steps, highlight the issue, and compare Prisma with its counterparts.

---

## Step-by-Step Guide

### 1. Understanding the requirement
### 1.1 The Custom Ordering Requirement

Modern ORMs offer a convenient way to sort by specific fields or enum indices. This is especially handy when you have an enum that represents a strict order (like priority levels: `LOW`, `MEDIUM`, `HIGH`).

- **Why this matters**: If you want items always ordered by `HIGH` first, then `MEDIUM`, then `LOW`, you might rely on the enum’s internal indexing or a custom field to achieve consistent sorting.

### 1.2 How Prisma Typically Handles Mapped Fields

Prisma maps fields in your schema (e.g., `someField`) to columns in your database (e.g., `some_field`). Under most circumstances, Prisma automatically translates the naming between your application layer (camelCase) and your database layer (snake_case).

- **What’s great about Prisma**: This abstraction simplifies your code, so you don’t have to worry about the underlying column names.

### 3. The Drawback: Using Raw SQL for Custom Enum Order

When you need to order by an enum’s indexed order that isn’t directly supported by Prisma’s standard query capabilities, you might resort to a custom raw SQL query:

```typescript
const query = Prisma.$queryRaw`
    SELECT * 
    FROM tasks
    ORDER BY 
      CASE 
        WHEN severity = HIGH THEN 1
        WHEN severity = NORMAL THEN 2
        WHEN severity = LOW THEN 3
        ELSE 999999
      END,
      created_at DESC
  `;
```


- **The issue**: The result returned from `prisma.$queryRaw` will not automatically map column names from `snake_case` to `camelCase`. This means you end up with fields like `my_enum_column` in your JavaScript objects instead of the expected `myEnumColumn`.

### 4. Why This Becomes a Problem

When your application logic expects a property named `myEnumColumn` (camelCase), but the database returns `my_enum_column` (snake_case), you have to manually handle that conversion. This extra step can introduce errors or force you to create unnecessary helper functions to remap fields.

- **Inconsistent Code**: You’ll have to remember which parts of your code deal with raw SQL (snake_case) versus Prisma’s default query system (camelCase).
- **Extra Boilerplate**: You might add transformations to your service layer just to rename fields properly.

### 5. Alternatives in TypeORM or MikroORM

Other ORMs such as **TypeORM** or **MikroORM** often provide decorators or built-in mechanisms to handle custom ordering directly in their query builder or entity configuration.

- **TypeORM**:
```typescript
createQueryBuilder(Task, "task")
  .orderBy(`
    CASE 
      WHEN severity = "HIGH" THEN 1
      WHEN severity = "MEDIUM" THEN 2
      WHEN severity = "LOW" THEN 3
    END
  `, 'ASC')
  .getMany();
```

	    - Even if it involves writing some SQL logic, TypeORM’s query builder often keeps the field mapping in sync with your entity definitions.
    
- **MikroORM**:
```typescript
const caseExpression = `
    CASE 
      WHEN severity = "HIGH" THEN 1
      WHEN severity = "MEDIUM" THEN 2
      WHEN severity = "LOW" THEN 3
    END
  `;
const allTasks = await em.find(Task, {}, { orderBy: { [raw(caseExpression)]: 'ASC' } }); 
```

		- MikroORM’s entity definitions and naming strategies often ensure consistent field naming without requiring raw SQL queries.

- **Why They Might Work Better**: In these ORMs, you often define the enum fields and the ordering logic in your entity configuration. If you write a custom expression, you’re still able to leverage the ORM’s mapping layer, thus avoiding the mismatch of `camelCase` vs. `snake_case`.

### 6. My Workarounds in Prisma

If you’re heavily invested in Prisma and still want to achieve custom enum ordering without dealing with mismatched field names:
    Write a small utility function that converts `snake_case` keys to `camelCase` after you execute a raw SQL query, ensuring consistency.
    
    - Check out libraries like `lodash`’s `camelCase` or `camelize` for quick conversions

---

## Conclusion

- **Recap of the Process**  
    Handling custom enum ordering is a common need in many applications. With TypeORM or MikroORM, you can often maintain a consistent field mapping while still using a flexible query builder for sorting. Prisma, however, requires a raw SQL approach for more complex enum sorting logic, leading to a mismatch between `camelCase` properties in your application and `snake_case` columns in your database.
    
- **Final Thoughts or Additional Tips**  
    I encourage you to try out these steps and see which approach works best in your application. Try to avoid Prisma on production, you can consider 2 options above to make your application get thing done.
