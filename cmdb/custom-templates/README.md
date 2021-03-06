## Custom templates

##### It's possible to create custom templates to build completely different CMDBs or to enhance the existing ones. Ansible-cmdb uses the Mako templating engine to render output.

##### For example, if you want to add a custom column to the `html_fancy` template (note that it's easier to just use the `--cust-cols` option. For more info see above): 

##### 1. Make a copy of the default `html_fancy` template in a new dir. Here, we'll use files from the ansible-cmdb install location.

```
mkdir ~/mytemplates
cp /usr/local/lib/ansiblecmdb/data/tpl/html_fancy.tpl ~/mytemplates
cp /usr/local/lib/ansiblecmdb/data/tpl/html_fancy_defs.html ~/mytemplates
```

##### 2. Edit the `html_fancy_defs.html` file and add an entry to the cols = section. In this example, we'll add a column for the "My Column".

```
  cols = [
    {"title": "Name",          "id": "name",          "func": col_name,           "sType": "string", "visible": True},
    {"title": "Groups",        "id": "groups",        "func": col_groups,         "sType": "string", "visible": False},
    {"title": "FQDN",          "id": "fqdn",          "func": col_fqdn,           "sType": "string", "visible": True},
    {"title": "CRON Status",   "id": "cronstatus",   "func": col_cronstatus,       "sType": "string", "visible": True},
    {"title": "My Data",       "id": "mydata",        "func": col_mydata,         "sType": "string", "visible": True}

  ]
```
##### 3. Now you need to implement the `col_my_column` template function. In the same `html_fancy_defs.html` file, search for the ## Column functions section. Add a column template function called `col_my_column`:

```

<%def name="col_cronstatus(host, **kwargs)">
  ${jsonxs(host, 'ansible_facts.ansible_local.cron-status.cron_status', default='')}
</%def>

<%def name="col_mydata(host, **kwargs)">
  ${jsonxs(host, 'ansible_facts.ansible_local.mydata', default='')}
</%def>

```
##### 4. Deploy the custom fatc scripts to target servers

```
ansible-playbook -i /home/ansiblebot/inv configure-custom-facts.yml
```

##### 5. Finally, render the custom template. For this to work, you must be in the same directory as the custom template!.

```
ansible-cmdb -t ./html_fancy -i inv out/ > custom-data.html
```
