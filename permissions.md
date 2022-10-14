# Permissions API

## List permissions

### List of available permissions for the external network at "CONNECT-DOMAIN"

{% swagger src=".gitbook/assets/api.yaml" path="/api/v1/customer/permissions" method="get" %}
[api.yaml](.gitbook/assets/api.yaml)
{% endswagger %}

## List advisor's permissions (recommended)

### List the permissions granted to an advisor on an external network.

{% swagger src=".gitbook/assets/api.yaml" path="/api/v2/customer/advisors/{advisorSymphonyId}/externalNetwork/{externalNetwork}/permissions" method="get" %}
[api.yaml](.gitbook/assets/api.yaml)
{% endswagger %}

## Add permission to an advisor (recommended)

### Grant permission to an advisor.

#### Refer to the top of this page, Permission table, to consult the default permissions.

#### For a Multi-company-contacts, the contacts must be from the same network, e.g. all from WeChat or all from WhatsApp. Cross network rooms are not authorized.

{% swagger src=".gitbook/assets/api.yaml" path="/api/v2/customer/advisors/{advisorSymphonyId}/externalNetwork/{externalNetwork}/permissions" method="post" %}
[api.yaml](.gitbook/assets/api.yaml)
{% endswagger %}

### List advisor's permissions (deprecated)

Please note the endpoint is **"/api/v1/customer/advisors/advisorEmailAddress/**_{advisorEmailAddress}_**/externalNetwork/**_{externalNetwork}_**/permissions"**

{% swagger src=".gitbook/assets/api.yaml" path="/api/v1/customer/advisors/advisorEmailAddress/{advisorEmailAddress}/externalNetwork/{externalNetwork}/permissions" method="get" %}
[api.yaml](.gitbook/assets/api.yaml)
{% endswagger %}

### Add permission to an advisor (deprecated)

{% swagger src=".gitbook/assets/api.yaml" path="/api/v1/customer/advisors/advisorEmailAddress/{advisorEmailAddress}/externalNetwork/{externalNetwork}/permissions" method="post" %}
[api.yaml](.gitbook/assets/api.yaml)
{% endswagger %}

## Remove permission (recommended)

### Remove a permission from an advisor.

{% swagger src=".gitbook/assets/api.yaml" path="/api/v2/customer/advisors/{advisorSymphonyId}/externalNetwork/{externalNetwork}/permissions/{permissionName}" method="delete" %}
[api.yaml](.gitbook/assets/api.yaml)
{% endswagger %}

## Remove Permission from an Advisor (deprecated)

### Remove Permission from an Advisor.

{% swagger src=".gitbook/assets/api.yaml" path="/api/v1/customer/advisors/advisorEmailAddress/{advisorEmailAddress}/externalNetwork/{externalNetwork}/permissions/{permissionName}" method="delete" %}
[api.yaml](.gitbook/assets/api.yaml)
{% endswagger %}

## Add permission to multiple advisors (recommended)

### Grant permission to multiple advisors.

{% swagger src=".gitbook/assets/api.yaml" path="/api/v2/customer/advisors/permissions" method="post" %}
[api.yaml](.gitbook/assets/api.yaml)
{% endswagger %}
