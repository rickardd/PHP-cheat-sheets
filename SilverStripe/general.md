# Silverstripe cheat sheet

## PHP


### controller

```php
class ShowsPage_Controller extends Page_Controller {

  public function init()
  {
    parent::init();
  }

}
```

### page
```php
class ShowsPage extends Page {

}
```

### allowens
 
```php
// Allowed children on pages in the cms. 
private static $allowed_actions = [];
```

```php
// disalowes this page-type to be on root level
private static $can_be_root = false;
```

```php
// Allowed chirend on pages in the cms. 
private static $allowed_children = array(
  'NameOfClildPage'
);
```


### Add fields to tab

```php
public function getCMSFields(){
  $fields = parent::getCMSFields(); // Always use this style to get all the scafolding. 

  return $fields;
}
```

```php
// Each field will be presented in the same order as the code. 
// The has one relationships e.g photo will be placed at the bottom of the cms... unless...
// 'Metadata' is defied
$fields->addFieldsToTab(
  'Root.Main',
  [
    TextField::create( 'Title', 'Show title' ),
    TextAreaField::create( 'Content', 'Description of the show'), 
    UploadField::create( 'Photo', 'Upload a photo for the show'), // This will be placed at the bottom cause it's a $has_one unless 'Metadata' is defined
  ],
  'Metadata' // NOTE: if this class extends DataObject this will break since no Metadata exists on DataObject. 
);
```

```php
    $fields->addFieldToTab('Root.Main', TextField::create( 'Title', 'Show title' ), 'Metadata' );
    $fields->addFieldToTab('Root.Main', TextAreaField::create( 'Content', 'Description of the show', 'Metadata' )); 
    $fields->addFieldToTab('Root.Main', UploadField::create( 'Photo', 'Upload a photo for the show'), 'Metadata'); 
```


### Database tables, collumns and relation ships

```php
  private static $db = array(
    'Title' => 'Varchar',
    'Content' => 'Text',
  );

  private static $has_many = array(
    'Prices' => 'Price',
    'Dates' => 'ShowDate',
    'Workers' => 'Worker',
  );

  private static $has_one = array(
    'Photo' => 'Image',
    'ShowsPage' => 'ShowsPage',
    'Booking' => 'Booking',
  );
```


### ModelAdmin

Create the Model

```php
class Place extends DataObject   
{
    private static $db = [ ... ];

    private static $has_one = [ ... ]; // This will be represented as a dropdown in the cms. 

    public function getCMSFields(){ ... }

    private static $summary_fields = [ ... ];

    // By default everything is searchable 
    // Specify the fields that should be searchable. 
    private static $searchable_fields = array (
      'Country',
      'Region',
      'Price',
    ); 
}
```

```php
class PlaceAdmin extends ModelAdmin {

    private static $menu_title = 'Place'; 

    // Required 
    private static $url_segment = 'places'; // accessible on www.mysite.com/admin/places

    // Required
    private static $managed_models = ['Place']; // The reference to the Model

    private static $menu_icon = 'mysite/icons/places.png';    
}
```


### DB Queries

```php
public function GetBookings() {
  return Booking::get()->filter( array( 'ShowID' => $this->ID ) )->sort('Name');
}
```

### Form


```php
public function BookinForm() {
  $form = Form::create(
      $this, // controller
      __FUNCTION__, // this is the same as hardcoding 'BookingForm'. NOTE: this has to be an $allowed_action
      // input fields
      FieldList::create(
          TextField::create('Name','')
              ->setAttribute('placeholder','Full Name'),
          EmailField::create('Email','')
              ->setAttribute('placeholder','Email')
      ),
      // action fileds
      FieldList::create(
          FormAction::create('onSubmit','Post Booking')
              ->setUseButtonTag(true)
      ),
      // Validation
      RequiredFields::create('Name','Email')
  );

  return $form;
}

public function onSubmit($data, $form) {
  
  $booking = Booking::create();
  $booking->Name = $data['Name'];
  $booking->Email = $data['Email'];
  $booking->Comment = $data['Comment'];
  $booking->ShowID = $this->ID;

  $booking->write();

  return $this->redirectBack();
}

```


```php
->setConfig('showcalendar', true), 'Content')
```

```php
public function init()
    {
        parent::init();
        
        Requirements::css("{$this->ThemeDir()}/css/style.css");
        Requirements::javascript("{$this->ThemeDir()}/js/common/modernizr.js");
    }
```

```php
private static $searchable_fields = array (
    'Title',
    'Region.Title',
    'FeaturedOnHomepage'
);  
```

### Grid field

```php
$fields->addFieldToTab('Root.Regions', GridField::create(
      'Regions', 
      'Regions on this page',
      $this->Regions(),
      GridFieldConfig_RecordEditor::create()
    ));
```

### Routing and Redirects

```php
class RegionsPage_Controller extends Page_Controller {

  private static $allowed_actions = array (
    'show'
  );

  public function show(SS_HTTPRequest $request) {
    $region = Region::get()->byID($request->param('ID'));

    if(!$region) {
        return $this->httpError(404,'That region could not be found');
    }

    return array (
        'Region' => $region,
        'Title' => $region->Title
    );
  }
}
```

### Extensons

#### The extension file

```php
class BookingFormExtension extends DataExtension {
  
  private static $allowed_actions = ['BookForm'];

  public function Foo() { // will be available in the .ss files
    $this->owner, // referese to the instance of the class it extends from. 
  }

  public $Bar = true; // will be available in the .ss files

}

```

***NOTE:*** use ``$this-.owner``` to refere to the instance of the class which is extending this. 

#### Set up the _config.yml

```
ShowPage_Controller: // Where to implement the extension into
  extensions:
    - BookingFormExtension // What extension to implement
```

***Flush the cash***

Any public method or variable will now be accessible on the .ss templates. 

```php
$fields->addFieldToTab('Root.Attachemnts', $photo = UploadField::create('Photo'));
      
$photo->getValidator()->setAllowedExtensions(Array('png', 'gif', 'jpg', 'jpeg'));
$photo->setFolderName('travel-photos');
```

```php
  public static $allowed_actions = array(
    'CommentForm'
  );
```

```php
Property::get()
  ->filter(array(
      'FeaturedOnHomePage' => true
    ))
  ->limit(6);
```

### Sessions
```php
Session::get("FormData.{$form->getName()}.data");
```

```php
Session::set("FormData.{$form->getName()}.data", $data);
```

```php
Session::clear("FormData.{$form->getName()}.data");
```

```php
$form->sessionMessage('Thanks for you comment', 'good');
```

### Form
```php
return $data ? $form->loadDataFrom($data) : $form;
```

```php
// To redirect back to same page after form submition. 
return $this->redirectBack();
```

```php
$comment = ArticleComment::create();
// $comment->Name = $data['Name'];
// $comment->Email = $data['Email'];
// $comment->Comment = $data['Comment'];
$comment->ArticlePageID = $this->ID;
$form->saveInto($comment); // This will do the same as commented 3 lines above.
$comment->write();
```

```php
  public function LinkingMode() {
      return Controller::curr()->getRequest()->param('ID') == $this->ID ? 'current' : 'link';
  }
```


### Admin model

```php
class PropertyAdmin extends ModelAdmin {

    private static $menu_title = 'Properties';

    private static $url_segment = 'properties';

    private static $managed_models = array (
        'Property'
    );

    private static $menu_icon = 'mysite/icons/property.png';    
}
```

### Pagination

```php
// controller
public function index(SS_HTTPRequest $request) {
  
    $shows = ShowPage::get();

    $paginatedShows = PaginatedList::create(
        $shows,
        $request
    )->setPageLength(2);

    return array (
        'Results' => $paginatedShows
    );
}
```

```html

<!-- Template.ss -->
<% if $Results.MoreThanOnePage %>

<div>
    <% if $Results.NotFirstPage %>
    <ul>
        <li><a href="$Results.PrevLink"> < </a></li>
    </ul>
    <% end_if %>
    <ul>
        <% loop $Results.Pages %>
        <li <% if $CurrentBool %>class="active"<% end_if %>><a href="$Link">$PageNum</a></li>
        <% end_loop %>
    </ul>
    <% if $Results.NotLastPage %>
    <ul >
        <li><a href="$Results.NextLink"> > </a></li>
    </ul>
    <% end_if %>
</div>
<% end_if %>
```



## .ss

### Child pages

```
// looping child pages
<% loop $Children %>
```

### Menu
```
<ul>
    <% loop $menu(1) %> 
    <li><a class="$LinkingMode" href="$Link">$MenuTitle</a></li>
    <% end_loop %>
</ul>
```

### SS methods

```
$Content.FirstSentence
$Brochure.URL
$Brochure.Extension
$Brochure.Size
$Commnets.Created.Format('j F, Y')
$PricePerNight.Nice
$PrimaryPhoto.CroppedImage(220, 194)
```

### SS variables

```
$ThemeDir
$Breadcrumbs // silverstripe native variable?
$AbsoluteBaseURL // ??
```

### Meta tags
```            
// set custom title???
$MetaTags(false)
<title>$Title</title>
```



## Configurations .yml


### Change theme
```
SSViewer:
  theme: 'one-ring'
```

### Adding extension

```
SiteConfig:
  extensions:
    - SiteConfigExtension
```
