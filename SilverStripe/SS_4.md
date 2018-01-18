# SS 4

Examples of what's different between ss3 and ss4

- Everything is name spaced, even in the yml files. 

## SiteSettings


```ylm
# _config/mysite.yml
SilverStripe\SiteConfig\SiteConfig:
  extensions:
    - SiteSettingsExtension
```

```php
// mysite/code/models/SiteSettingsExtension.php
<?php 

use SilverStripe\ORM\DataExtension;
use SilverStripe\Forms\GridField\GridField;
use SilverStripe\Forms\GridField\GridFieldConfig_RecordEditor;

class SiteSettingsExtension extends DataExtension {

    private static $has_many = [
      'FooterLinks' => 'FooterLink',
    ];

    public function updateCMSFields( SilverStripe\Forms\FieldList $fields ){
    
        $fields->addFieldsToTab(
          'Root.FooterLinks',
          [
            GridField::create(
              'FooterLinks', 
              'Links to be displayed in the footer',
              $this->owner->FooterLinks(),

              GridFieldConfig_RecordEditor::create()
            )
              
          ]
        );

      }  
  
}

// mysite/code/models/FooterLink.php

<?php

use SilverStripe\ORM\DataObject;
use SilverStripe\Forms\TextField;
use SilverStripe\Forms\TreeDropdownField;

class FooterLink extends DataObject
{

    private static $db = [
        'Title' => 'Varchar(255)',
        'Link' => 'Varchar(255)',
    ];

    private static $has_one = [
        'Page' => 'SilverStripe\CMS\Model\SiteTree',
        'SiteConfig' => 'SilverStripe\SiteConfig\SiteConfig',
    ];

    private static $summary_fields = [
        'Title' => 'Title',
        'Link' => 'Link',
    ];

    public function getCMSFields()
    {
        $fields = parent::getCMSFields();

        $fields->addFieldsToTab(
          'Root.Main',
          [
            TextField::create( 'Title' ),
            TreeDropdownField::create('PageLinkID', 'Link to Page', 'Page')
          ]
        );

        return $fields;
    }
}

```

## Setting up KSS styleguide

```yml
# mysite/_config/routes.yml
---
Name: mysiteroutes
After: framework/routes#coreroutes
---

---
Only:
  environment: 'dev'
---

SilverStripe\Control\Director:
  rules:
    'styleguide/$Action': 'StyleguideController'
```

```php
<?php

use SilverStripe\CMS\Controllers\ContentController;
use SilverStripe\Control\HTTPRequest;

class StyleguideController extends ContentController
{
    public function index(HTTPRequest $request)
    {
        $page = $request->param('Action');

        $this->renderStyleguide(($page) ? $page . '.html' : 'index.html');
    }

    private function renderStyleguide($page)
    {
        echo file_get_contents('themes/default/styleguide/' . $page);
    }
}


```
    

