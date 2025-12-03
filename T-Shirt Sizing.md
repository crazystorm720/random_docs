## T-Shirt Sizing Framework

Here's a comprehensive table with common solution components:

| **Office Size** | **User Count** | **Device Category** | **Specific Device** | **Quantity** | **Hardware SKU** | **License SKU** | **Estimate ID** | **Monthly Cost** |
|-----------------|----------------|---------------------|---------------------|--------------|------------------|-----------------|-----------------|------------------|
| **Small Office** | 5-20 users | Edge Security/Firewall | MX68 | 1 | MX68-HW | LIC-MX68-ENT-1Y | MX158405510LQ | $75 |
| **Small Office** | 5-20 users | Wireless Access Point | MR28 | 1-2 | MR28-HW | LIC-ENT-1YR | MX158405510LQ | $25/ea |
| **Small Office** | 5-20 users | Switch (PoE) | MS120-8P | 1 | MS120-8P-HW | LIC-MS120-8-1Y | MX158405510LQ | $40 |
| **Small Office** | 5-20 users | Security Camera | MV12W | 2-3 | MV12W-HW | LIC-MV12W-1Y | MX158405510LQ | $15/ea |
| **Small Office** | 5-20 users | VoIP Phone | MP43 | 5-20 | MP43-HW | - | MX158405510LQ | - |
| **Medium Office** | 21-50 users | Edge Security/Firewall | MX75 | 1 | MX75-HW | LIC-MX75-ENT-1Y | DL158405516XC | $150 |
| **Medium Office** | 21-50 users | Wireless Access Point | MR36 | 2-3 | MR36-HW | LIC-ENT-1YR | DL158405516XC | $40/ea |
| **Medium Office** | 21-50 users | Switch (PoE+) | MS125-24P | 1-2 | MS125-24P-HW | LIC-MS125-24-1Y | DL158405516XC | $80/ea |
| **Medium Office** | 21-50 users | Security Camera | MV22 | 3-5 | MV22-HW | LIC-MV22-1Y | DL158405516XC | $25/ea |
| **Medium Office** | 21-50 users | VoIP Phone | MP48 | 21-50 | MP48-HW | - | DL158405516XC | - |
| **Medium Office** | 21-50 users | SD-WAN Router | vMX100 | 1 | VIRT-MX100 | LIC-vMX100-1Y | DL158405516XC | $100 |
| **Large Office** | 51-200 users | Edge Security/Firewall | MX95 | 1-2 | MX95-HW | LIC-MX95-ENT-1Y | IS158405517RZ | $300 |
| **Large Office** | 51-200 users | Wireless Access Point | MR46 | 3-6 | MR46-HW | LIC-ENT-1YR | IS158405517RZ | $60/ea |
| **Large Office** | 51-200 users | Switch (Aggregation) | MS250-48P | 1-2 | MS250-48P-HW | LIC-MS250-48-1Y | IS158405517RZ | $150/ea |
| **Large Office** | 51-200 users | Switch (Access) | MS130-48P | 2-3 | MS130-48P-HW | LIC-MS130-48-1Y | IS158405517RZ | $100/ea |
| **Large Office** | 51-200 users | Security Camera | MV72 | 5-10 | MV72-HW | LIC-MV72-1Y | IS158405517RZ | $40/ea |
| **Large Office** | 51-200 users | Environmental Sensor | MT10 | 2-3 | MT10-HW | LIC-MT10-1Y | IS158405517RZ | $15/ea |
| **Large Office** | 51-200 users | VoIP Phone | MP75 | 51-200 | MP75-HW | - | IS158405517RZ | - |
| **Large Office** | 51-200 users | SD-WAN Router | vMX250 | 1 | VIRT-MX250 | LIC-vMX250-1Y | IS158405517RZ | $200 |
| **Large Office** | 51-200 users | Advanced Security | MX-THREAT | 1 | MX-THREAT-HW | LIC-THREAT-1Y | IS158405517RZ | $125 |

## How Liquid Transforms This Into Dynamic Templates

Here's where Liquid becomes magical for your use case:

### 1. **Dynamic Quantity Calculation**
```liquid
{% if office_size == "small" %}
  {% assign user_count = 15 %}
  {% assign phone_quantity = user_count %}
  {% assign ap_quantity = user_count | divided_by: 15 | ceil %}
  {% assign camera_quantity = user_count | divided_by: 7 | ceil %}
{% elsif office_size == "medium" %}
  {% assign user_count = 35 %}
  {% assign phone_quantity = user_count %}
  {% assign ap_quantity = user_count | divided_by: 25 | ceil %}
  {% assign camera_quantity = user_count | divided_by: 10 | ceil %}
{% endif %}
```

### 2. **Conditional Components Based on Requirements**
```liquid
{% if solution_requirements contains "security_cameras" %}
  <tr>
    <td>Security Camera</td>
    <td>MV{{ camera_model }}</td>
    <td>{{ camera_quantity }}</td>
    <td>MV{{ camera_model }}-HW</td>
    <td>LIC-MV{{ camera_model }}-1Y</td>
  </tr>
{% endif %}

{% if solution_requirements contains "voip" %}
  <tr>
    <td>VoIP Phone</td>
    <td>MP{{ phone_model }}</td>
    <td>{{ user_count }}</td>
    <td>MP{{ phone_model }}-HW</td>
    <td>-</td>
  </tr>
{% endif %}
```

### 3. **Total Cost Calculation**
```liquid
{% assign total_monthly_cost = 0 %}
{% assign total_hardware_cost = 0 %}

{% for item in solution_items %}
  {% assign item_monthly = item.quantity | times: item.monthly_cost %}
  {% assign item_hardware = item.quantity | times: item.hardware_cost %}
  
  {% assign total_monthly_cost = total_monthly_cost | plus: item_monthly %}
  {% assign total_hardware_cost = total_hardware_cost | plus: item_hardware %}
  
  <tr>
    <td>{{ item.device }}</td>
    <td>{{ item.quantity }}</td>
    <td>${{ item_hardware | money_without_currency }}</td>
    <td>${{ item_monthly | money_without_currency }}/mo</td>
  </tr>
{% endfor %}

<tr class="total-row">
  <td colspan="2"><strong>Totals</strong></td>
  <td><strong>${{ total_hardware_cost | money_without_currency }}</strong></td>
  <td><strong>${{ total_monthly_cost | money_without_currency }}/mo</strong></td>
</tr>
```

### 4. **Complete Solution Template Example**
```liquid
{% comment %} 
Solution Template: {{ template_name }}
For: {{ client_name }}
Office Size: {{ office_size }}
Users: {{ user_count }}
Requirements: {{ requirements | join: ", " }}
{% endcomment %}

{% assign config = site.solution_templates[office_size] %}

## Solution Quote: {{ client_name }}

**Office Size:** {{ office_size | upcase }}  
**Estimated Users:** {{ user_count }}  
**One-Time Hardware:** ${{ config.hardware_total | money_without_currency }}  
**Monthly Licensing:** ${{ config.monthly_total | money_without_currency }}  
**Implementation Services:** ${{ config.services_total | money_without_currency }}

### Bill of Materials

| Category | Device | Qty | Hardware Cost | License/Mo |
|----------|--------|-----|---------------|------------|
{% for item in config.items %}
| {{ item.category }} | {{ item.name }} | {{ item.quantity }} | ${{ item.hardware_cost }} | {% if item.license_cost %}${{ item.license_cost }}/mo{% else %}-{% endif %} |
{% endfor %}

**Total One-Time:** ${{ config.hardware_total }}  
**Monthly Recurring:** ${{ config.monthly_total }}

{% if requirements contains "installation" %}
### Professional Services
- Site Survey & Design
- Hardware Installation
- Configuration & Testing
- User Training
{% endif %}
```

## Practical Applications with Liquid

1. **Shopify for Service Providers**: Create a "Solution Configurator" app where clients can select their office size and requirements, generating dynamic quotes.

2. **Jekyll Static Site**: Build a catalog of solution templates that can be easily updated and customized.

3. **Internal Tooling**: Create quote generators for sales teams that automatically pull pricing and SKUs.

4. **Proposal Automation**: Generate complete proposals with conditional sections based on selected options.

## Recommended Architecture

```yaml
# _data/solution_templates.yml
small_office:
  name: "Small Office"
  user_range: "5-20"
  devices:
    firewall:
      model: "MX68"
      base_quantity: 1
      hardware_sku: "MX68-HW"
      license_sku: "LIC-MX68-ENT-1Y"
      monthly_cost: 75
    access_points:
      model: "MR28"
      quantity_per_users: 15
      hardware_sku: "MR28-HW"
      license_sku: "LIC-ENT-1YR"
      monthly_cost: 25
    # ... more devices
```

**Benefits of this approach:**
- Consistent pricing/quotes across sales team
- Easy to update SKUs or pricing in one place
- Automated calculations reduce errors
- Can generate PDF quotes, web pages, or shopping carts
- Scales to hundreds of solution variations

Would you like me to help you build a complete Liquid template system for this, or do you need help with specific aspects like dynamic pricing calculations or conditional logic?
