# gofetch-goexplore-a-superuser-tools

This [Gofetch](https://goaddon.com/en/addons/5b9ff6463ab42f43522b30cf) repository deploys a handy set of pages on websites hosted by [Goexplore](https://goaddon.com/en/addons/5bb227d283c3360abe01e036)'s A framework, that enables superusers to:

- Handle support tickets opened by merchants through the Gofetch interface
- Make requests to a webshop through the Gofetch Shop API
- View merchants unfinished or failed integration attempts (auths)

## Branches

The repository has two branches:

- a1-main_menu assigns its menu entries to the main menu. Since main menu entries are displayed to all users, but these entries are only relevant for superusers, this branch should only be deployed if there are no regular users.
- a1-superuser_menu assigns its menu entries to the SUPERUSER menu, which is only displayed to superusers.

## Adding your own pages

This main functionality in this repository centers around three pages: *webshops__index*, *auths__index* and *tickets__index*.

Each index action breaks down all corresponding documents in your database.

Each document will be presented as a row in a table. For each row relevant links are presented, but if you want to add a link without having to modify the code you can add a secret in your Goaddon account under [GOEXPLORE > SECRETS](https://goaddon.com/en/addons/5bb227d283c3360abe01e036/manage#page=secrets).

If, for example, you want to add a page that informs you whether an account currently subscribes to your paid services, you could provide a link from the *webshops__index* page, since all webshops belongs to an account. Your secret could then be:

Key: `table_cols_webshops__index`
Value: `subscriptions__index;Subscriptions;fas fa-tags`

The value consists of three elements, separated by `;`. The first is the path, the next is the link text (which can not be localized) and the last is the class of a [FontAwesome](https://fontawesome.com/) icon, which will be displayed in narrow browser windows.

Assuming that you are keeping track of the subscription status in a `subscribed` attribute of the account document, you could create this page:

```html
{% if account != nil and user.superuser %}

  <h5 class="mb-4">{{ l.headline }}</h5>

  <p>
    {% if account.subscribed %}
      {{ l.subscribed }} <i class="fas fa-check-circle text-success"></i>
    {% else %}
      {{ l.not_subscribed }} <i class="fas fa-exclamation-circle text-danger"></i>
    {% endif %}
  </p>

{% endif %}
```

with these locales:

```json
{
  "en": {
    "headline": "Subscription status",
    "subscribed": "Subscribed",
    "not_subscribed": "Not subscribed"
  }
}
```

To connect the page to your menu structure, whithout needing to create a main meny entry, you can append to the ACCOUNT entry by creating a *superuser__subscriptions* menu entry, for example with this title:

```json
{
  "en": "Subscriptions"
}
```

and these paths:

```json
[
  {
    "controller": "subscriptions",
    "action": "index"
  }
]
```

## Sending emails to external recipients

If you are using the ticketing functionality to communicate with external recipients, such as the owners of an account, you need to [register your SMTP settings](https://goaddon.com/en/addons/5bb227d283c3360abe01e036/manage#page=smtp) with Goexplore.

Every time you send a message, the recipients will receive a notification email with the message, but this email will not include previous messages.

The email will be based on the email template `external_ticket_message.html`. This email template is as generic as it could be, so you may want to override with a template of your own. To do that you need to create it with the following in mind:

- The first line of your HTML will be used as subject line, as per standard Goexplore conventions.
- Your template will have access to `params.ticket_url`, which will contain a link to the full conversation history of the ticket. Please remember to include it, similar to how we have done in our template.
- Your best starting point would be to copy our template entirely, and subsequently refactor it to fit your needs.