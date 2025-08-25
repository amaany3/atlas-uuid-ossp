# atlas-uuid-ossp

ref: https://atlasgo.io/faq/postgres-extensions

## Version

```
❯ atlas version
atlas version v0.36.0
https://github.com/ariga/atlas/releases/tag/v0.36.0
```

## Steps to Reproduce

```
❯ docker compose up -d

❯ atlas schema diff \
  --from "postgresql://postgres:pgpass@localhost:5432/postgres?sslmode=disable" \
  --to "file://schema.sql" \
  --dev-url docker://postgres/15/dev
-- Create "customers" table
CREATE TABLE "public"."customers" (
  "id" uuid NOT NULL DEFAULT public.uuid_generate_v4(),
  "first_name" character varying(100) NOT NULL,
  "last_name" character varying(100) NOT NULL,
  "email" character varying(255) NOT NULL,
  PRIMARY KEY ("id"),
  CONSTRAINT "customers_email_key" UNIQUE ("email")
);
-- Create "products" table
CREATE TABLE "public"."products" (
  "id" uuid NOT NULL DEFAULT public.uuid_generate_v4(),
  "name" character varying(255) NOT NULL,
  "description" text NULL,
  "price" numeric(10,2) NOT NULL,
  PRIMARY KEY ("id")
);
-- Create "orders" table
CREATE TABLE "public"."orders" (
  "id" uuid NOT NULL DEFAULT public.uuid_generate_v4(),
  "customer_id" uuid NULL,
  "order_date" timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  "status" character varying(50) NULL DEFAULT 'Pending',
  PRIMARY KEY ("id"),
  CONSTRAINT "orders_customer_id_fkey" FOREIGN KEY ("customer_id") REFERENCES "public"."customers" ("id") ON UPDATE NO ACTION ON DELETE CASCADE
);

❯ atlas schema apply \
  --url "postgresql://postgres:pgpass@localhost:5432/postgres?sslmode=disable" \
  --to "file://schema.sql" \
  --dev-url docker://postgres/15/dev
Error: analyzing plan: running first analysis: executing statement: CREATE TABLE "public"."customers" (
  "id" uuid NOT NULL DEFAULT public.uuid_generate_v4(),
  "first_name" character varying(100) NOT NULL,
  "last_name" character varying(100) NOT NULL,
  "email" character varying(255) NOT NULL,
  PRIMARY KEY ("id"),
  CONSTRAINT "customers_email_key" UNIQUE ("email")
);: pq: function public.uuid_generate_v4() does not exist
```
