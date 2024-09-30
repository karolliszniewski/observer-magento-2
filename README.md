# Complete Magento 2 HelloWorld Module with Events & Observers

Here's the complete file structure for the Mageplaza_HelloWorld module, including all necessary files:

```
app/code/Mageplaza/HelloWorld/
├── Controller/
│   └── Index/
│       └── Test.php
├── Observer/
│   └── ChangeDisplayText.php
├── etc/
│   ├── frontend/
│   │   ├── events.xml
│   │   └── routes.xml
│   ├── module.xml
│   └── di.xml
├── view/
│   └── frontend/
│       └── layout/
│           └── helloworld_index_test.xml
├── composer.json
└── registration.php
```

Now, let's go through each file and its contents:

1. `app/code/Mageplaza/HelloWorld/registration.php`

```php
<?php
\Magento\Framework\Component\ComponentRegistrar::register(
    \Magento\Framework\Component\ComponentRegistrar::MODULE,
    'Mageplaza_HelloWorld',
    __DIR__
);
```

2. `app/code/Mageplaza/HelloWorld/etc/module.xml`

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
    <module name="Mageplaza_HelloWorld" setup_version="1.0.0">
        <sequence>
            <module name="Magento_Backend"/>
        </sequence>
    </module>
</config>
```

3. `app/code/Mageplaza/HelloWorld/etc/frontend/routes.xml`

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:App/etc/routes.xsd">
    <router id="standard">
        <route id="helloworld" frontName="helloworld">
            <module name="Mageplaza_HelloWorld" />
        </route>
    </router>
</config>
```

4. `app/code/Mageplaza/HelloWorld/etc/frontend/events.xml`

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Event/etc/events.xsd">
    <event name="mageplaza_helloworld_display_text">
        <observer name="mp_display_text" instance="Mageplaza\HelloWorld\Observer\ChangeDisplayText" />
    </event>
</config>
```

5. `app/code/Mageplaza/HelloWorld/etc/di.xml`

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">
    <!-- Add any dependency injection configurations here if needed -->
</config>
```

6. `app/code/Mageplaza/HelloWorld/Controller/Index/Test.php`

```php
<?php
namespace Mageplaza\HelloWorld\Controller\Index;

use Magento\Framework\App\Action\Action;
use Magento\Framework\App\Action\Context;
use Magento\Framework\View\Result\PageFactory;
use Magento\Framework\DataObject;

class Test extends Action
{
    protected $resultPageFactory;

    public function __construct(
        Context $context,
        PageFactory $resultPageFactory
    ) {
        $this->resultPageFactory = $resultPageFactory;
        parent::__construct($context);
    }

    public function execute()
    {
        $textDisplay = new DataObject(array('text' => 'Mageplaza'));
        $this->_eventManager->dispatch('mageplaza_helloworld_display_text', ['mp_text' => $textDisplay]);
        
        $resultPage = $this->resultPageFactory->create();
        $resultPage->getConfig()->getTitle()->set(__('Hello World'));
        
        $block = $resultPage->getLayout()->getBlock('helloworld_display_text');
        $block->setText($textDisplay->getText());
        
        return $resultPage;
    }
}
```

7. `app/code/Mageplaza/HelloWorld/Observer/ChangeDisplayText.php`

```php
<?php
namespace Mageplaza\HelloWorld\Observer;

use Magento\Framework\Event\ObserverInterface;
use Magento\Framework\Event\Observer;

class ChangeDisplayText implements ObserverInterface
{
    public function execute(Observer $observer)
    {
        $displayText = $observer->getData('mp_text');
        $displayText->setText($displayText->getText() . " - Event");
        return $this;
    }
}
```

8. `app/code/Mageplaza/HelloWorld/view/frontend/layout/helloworld_index_test.xml`

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" layout="1column" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceContainer name="content">
            <block class="Magento\Framework\View\Element\Template" name="helloworld_display_text" template="Mageplaza_HelloWorld::test.phtml"/>
        </referenceContainer>
    </body>
</page>
```

9. `app/code/Mageplaza/HelloWorld/view/frontend/templates/test.phtml`

```php
<h1><?= $block->escapeHtml($block->getText()) ?></h1>
```

10. `app/code/Mageplaza/HelloWorld/composer.json`

```json
{
    "name": "mageplaza/module-hello-world",
    "description": "Mageplaza Hello World Module",
    "type": "magento2-module",
    "version": "1.0.0",
    "license": [
        "OSL-3.0",
        "AFL-3.0"
    ],
    "authors": [
        {
            "name": "Mageplaza",
            "email": "support@mageplaza.com",
            "homepage": "https://www.mageplaza.com",
            "role": "Technical Support"
        }
    ],
    "autoload": {
        "files": [
            "registration.php"
        ],
        "psr-4": {
            "Mageplaza\\HelloWorld\\": ""
        }
    }
}
```

These files constitute a complete Magento 2 module that implements the event and observer functionality we discussed earlier. The module includes proper routing, layout, and template files to display the result in a more Magento-friendly way.

To install and use this module:

1. Place these files in your Magento 2 installation under `app/code/Mageplaza/HelloWorld/`.
2. Run the following commands from your Magento 2 root directory:
   ```
   php bin/magento module:enable Mageplaza_HelloWorld
   php bin/magento setup:upgrade
   php bin/magento setup:di:compile
   php bin/magento cache:flush
   ```
3. Access the module at: `http://your-magento-url/helloworld/index/test`

This should display a page with the text "Mageplaza - Event".
