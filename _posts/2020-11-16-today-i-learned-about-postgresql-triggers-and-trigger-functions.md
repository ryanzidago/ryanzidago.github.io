---
layout: post
title:  "Today I learned about PostgreSQL's triggers and trigger functions"
date:   2020-11-16 17:45:00 +0100
categories: databases postgresql
tags: databases postgresql
---

## What are triggers and trigger functions?
According to PostgreSQL's [documentation](https://www.postgresql.org/docs/13/trigger-definition.html):
> A trigger is a specification that the database should automatically execute a particular function whenever a certain type of operation is performed. Triggers can be attached to tables (partitioned or not), views, and foreign tables.

Basically, you can tell PostgreSQL to execute a function anytime a row is inserted, updated, deleted from the database.

## When to use triggers and trigger functions?
After a rapid reseach on Google, it seems that te common uses cases for triggers are:
* audit logs ([here](https://www.postgresql.org/docs/10/plpgsql-trigger.html), [here](https://eager.io/blog/audit-postgres/) and [here](https://www.postgresqltutorial.com/creating-first-trigger-postgresql/)): to keep track of all the changes that happened in a table; to maintain a log of user activity for instance; when did the user update their first/last name, their email, their address, etc.
* constraints ([here](https://www.cybertec-postgresql.com/en/triggers-to-enforce-constraints/) and [here](http://johnwilger.com/2020/02/16/ecto-custom-unique-constraint-trigger.html)): to create constraints that cannot be implemented by the regular _constraint_ in PostgreSQL.

## Examples
I have found one example in the [EventStore](https://github.com/commanded/eventstore) library in Elixir: the EventStore [uses](https://github.com/commanded/eventstore/blob/33dea31fafe42bc89045dc0ae8ee6b5b9ae1f3e6/priv/event_store/migrations/v0.14.0.sql#L54) a trigger `event_notification` that is triggered after every update on the `streams` table. The `event_notification` trigger executes the `notify_event` trigger function (which itself calls the `pg_notify` function):

```sql
DO $$
BEGIN

/* ... */

  -- event pub/sub notification function
  CREATE OR REPLACE FUNCTION notify_events()
    RETURNS trigger AS $function$
  DECLARE
    payload text;
  BEGIN
      payload := NEW.stream_uuid || ',' || NEW.stream_id || ',' || (OLD.stream_version + 1) || ',' || NEW.stream_version;

      -- Notify events to listeners
      PERFORM pg_notify('events', payload);

      RETURN NULL;
  END;
  $function$ LANGUAGE plpgsql;

  -- event pub/sub table trigger
  CREATE TRIGGER event_notification
  AFTER UPDATE ON streams
  FOR EACH ROW EXECUTE PROCEDURE notify_events();

/* ... */

END;
$$ LANGUAGE plpgsql;
```

\
There is even a [cache](https://dspace.mit.edu/handle/1721.1/73895) build around PostgreSQL's triggers. When the data from the database changes, triggers are used to update the cache:
> While CacheGenie does not cache non-ORM operations, all updates to the database (through ORM and otherwise) are propagated to the cache through database triggers, ensuring cache consistency.
