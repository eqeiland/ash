# Code Interface

One of the ways that we interact with our resources is via hand-written code. The general pattern for that looks like building a query or a changeset for a given action, and dispatching it to the api using things like `MyApi.read/3` and `MyApi.create/3`. This, however, is just one way to use Ash, and is designed to help you build tools that work with resources, and to power things like `AshPhoenix.Form`, `AshGraphql.Resource` and `AshJsonApi.Resource`. When working with your resources in code, we generally want something more idiomatic and simple. For example, on a resource called `Helpdesk.Support.Ticket`:

```elixir
code_interface do
  define_for Helpdesk.Support

  define :open_ticket, args: [:subject]
end
```

This simple setup now allows you to open a ticket with `Helpdesk.Support.Ticket.open_ticket(subject)`. You can cause it to raise errors instead of return them with `Helpdesk.Support.Ticket.open_ticket!(subject)`. For information on the options and additional inputs these defined functions take, look at the generated function documentation, which you can do in iex with `h Helpdesk.Support.Ticket.open_ticket`. For more information on the code interface, read the DSL documentation: {{link:ash:dsl:resource/code_interface}}.

## Using the code interface

If the action is an update or destroy, it will take a record or a changeset as its *first* argument.
If the action is a read action, it will take a starting query as an *opt in the last* argument.

All functions will have an optional last argument that accepts options. Those options are:

#{Spark.OptionsHelpers.docs(Ash.Resource.Interface.interface_options(nil))}

For reads:

* `:query` - a query to start the action with, can be used to filter/sort the results of the action.

For creates:

* `:changeset` - a changeset to start the action with

They will also have an optional second to last argument that is a freeform map to provide action input. It *must be a map*.
If it is a keyword list, it will be assumed that it is actually `options` (for convenience).
This allows for the following behaviour:

```elixir
# Because the 3rd argument is a keyword list, we use it as options
Api.register_user(username, password, [tenant: "organization_22"])
# Because the 3rd argument is a keyword list, we use it as action input
Api.register_user(username, password, %{key: "val"})
# When all are provided it is unambiguous
Api.register_user(username, password, %{key: "val"}, [tenant: "organization_22"])
```