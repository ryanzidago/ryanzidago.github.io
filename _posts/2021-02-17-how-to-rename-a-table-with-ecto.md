```elixir
defmodule Example.Repo.Migrations.RenameTableCompanyToOrganization do
  use Ecto.Migration

  def up do
    rename table("companies"), to: table("organizations")
  end

  def down do
    rename table("organizations"), to: table("companies")
  end
end
```

Renaming a column is also easy:
```elixir
defmodule Example.Repo.Migrations.RenameNameColumnToLegalName do
  use Ecto.Migration

  def up do
    rename table("companies"), :name, to: :legal_name
  end

  def down do
    rename table("companies"), :legal_name, to: :name
  end
end
```
