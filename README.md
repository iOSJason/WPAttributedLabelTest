WPAttributedMarkup
==================

/**
*  WPAttributedMarkup是一个简单实用的分类category,可以通过标签和样式字典的方式来很容易的创建富文本
*/
WPAttributedMarkup is a simple utility category that can be used to easily create an attributed string from text with markup tags and a style dictionary.

/**
*  这个category允许你通过以下的这种方式来设置富文本
*/
The category allows you to turn text such as:

    <bold>Bold</bold> text

into  "<b>Bold</b>" text by using a style dictionary such as:

      NSAttributedString *as = [@"<bold>Bold</bold" attributedStringWithStyleBook:@{@"body":[UIFont fontWithName:@"HelveticaNeue" size:18.0],
      @"bold":[UIFont fontWithName:@"HelveticaNeue-Bold" size:18.0]}];


/**
在这个例子中,与"body"相关的样式,<bold>*******</bold>中所有的内容都被设置成字体对应的样式.

*/
In this example, the style associated with "body" is applied to the whole text, whereas the style associated with "bold" is applied to the text contained within < bold > tags.

/**
所有的标签都是用户自定义好的
*/
All tags (with the exception of 'body') are user-defined.

/**
这个称之为"样式书"的字典,里面包含了程序的字体,颜色等诸如此类的设置(可以在简单的地方进行辨识)
*/
The dictionary is called a 'style book' as the intention is for it to contain all of the application fonts, colours etc in a single place.


FAQ
---
/**
可以转化HTML文件成富文本吗?
*/
Q. Can this convert html to styled text?

/**
通常来说是不行的.虽然从句法上来看,两者很像(这个格式转化看起来像html风格的标签),但是所有的标签都是用户自定义的.如果html是标准格式的.或者是用严谨的语法来设置的完整的标签格式,你可以通过自定义标签的方式来讲html转换成富文本
*/
A. In general, no. The syntax is html-like, but all tags are user-defined. If the html is correctly formatted, and uses a strict set of tags, you could convert html to attributed text by defining an appropriate set of tags (b, h1, h2, etc).

/**
富文本不是本身就可以转化html格式的文件吗?就像这样:
*/
Q. Doesn't NSAttributedString already convert html text using something like:

    [NSAttributedString alloc] initWithData:[htmlString dataUsingEncoding:NSUTF8StringEncoding] 
                                 options:@{NSDocumentTypeDocumentAttribute: NSHTMLTextDocumentType,
                                           NSCharacterEncodingDocumentAttribute: @(NSUTF8StringEncoding)} 
                      documentAttributes:nil error:nil];

/**
*  富文本本身是支持直接将html进行转化的,但是上面的方法处理的非常缓慢,(它在后台使用了WebView)而且使用了异步执行(你可以试试在UITableView上快速滑动的效果),此外，任何样式必须在CSS文件中设置，而不是能够通过在你的应用程序已经使用标准颜色和字体类
*/
A. It does. However, the above approach is very slow (it uses a WebView in the background) and runs asynchronously (just try creating one in a UITableViewCell and scroll the table too fast...).
In addition, any styles must be set in the document css, rather than being able to pass in standard colour and font classes that your application already uses.

/**
*  支持什么样式呢?
*/
What styles are supported?
--
/**
*  以下的这些属性都可以被设置到属性字典中:
*/
The following objects can be used in the style dictionary:

//给文本设置颜色
<b>UIColor</b> : For colouring a section of text.


    @"red": [UIColor redColor]

//给文本设置字体
<b>UIFont</b> : For setting the font for a section of text.

    @"bold":[UIFont fontWithName:@"HelveticaNeue-Bold" size:18.0]

//根据键值对给文本设置样式.可以像常用的富文本那样用来设置下划线,中划线,段落样式等
<b>NSDictionary</b> : Applies an attributed string style key/pair to the section of text. This can be used for applying underline, strikethrough, paragraph styles, etc as well as custom attributes.


    @"u": @{NSUnderlineStyleAttributeName : @(kCTUnderlineStyleSingle|kCTUnderlinePatternSolid)}
//一个用来设置样式的数组,里面的所有方法都可以用来设置文本
<b>NSArray</b> : An array of any style items, all of which are applied to the section of text.

    @"u": @[[UIColor blueColor],
    @{NSUnderlineStyleAttributeName : @(kCTUnderlineStyleSingle|kCTUnderlinePatternSolid)} ]

//应用命名样式文本的部分。有用的，除了一些新的属性现有的样式而无需复制现有属性的应用
<b>NSString</b> : Applies a named style to the section of text. Useful for applying an existing style in addition to some new attributes without needing to copy the existing attributes.

     @"red": [UIColor redColor],
     @"redunderline": @[ @{NSUnderlineStyleAttributeName : @(kCTUnderlineStyleSingle|kCTUnderlinePatternSolid)}, @"red" ]
//在文本中插入一张图片,图像标签内的文字必须只包含一个字符，这将由图像所取代。
<b>UIImage</b> : Inserts the UIImage into the text at the section of text. Note: The text inside the image tags must contain only a single character, which will be replaced by the image. 

    @"thumb":[UIImage imageNamed:@"thumbIcon"]

Used in the text as:

     <thumb> </thumb>


Extras
--

 /// 还有些额外的很实用的可以和WPAttributedMarkup配合使用的类.WPHotspotLabel这个类可以被用来在block代码块中执行方法,用来设置富文本
Some extra utility classes are included which may also be of use in association with WPAttributedMarkup. In particular, the <b>WPHotspotLabel</b> class can be used to apply block methods to any part of the attributed string, allowing trivial application-specific links to be created.
    
/// WPAttributedStyleAction 如果定义了的话,这个类中封装了block，并使用<B > styledAction < / b>的方法允许插入一个属性字符串。这实际上增加了一个文字< b>link< / b>的超链接样式，所以文字也将继承在< b>link< / b>的样式
<b>WPAttributedStyleAction</b> - A class which wraps a block and allows insertion into an attributed string using the <b>styledAction</b> method. This actually adds a <b>link</b> style to the text, so the text will also inherit the attributed defined in the <b>link</b> style, if defined.

This can be used as follows:

    @"help":[WPAttributedStyleAction styledActionWithAction:^{
                                 NSLog(@"Help action");
                             }]

/// WPTappableLabel 一个简单的UILabel的子类,允许你设置点击事件
<b>WPTappableLabel</b> - A simple UILabel subclass which allows an onTap block to be set, which is called when the label is tapped.

/// WPHotspotLabel 是WPTappableLabel 的子类,用来检查属性点击区域,如果设置了点击事件的话,可以用来执行点击事件
<b>WPHotspotLabel</b> - A subclass of WPTappableLabel which detects the attributes of the text at the tapped position, and executes the action if a WPAttributedStyleAction attribute is found.

注意,这些类并没有经过完整的测试,所以,有时候某些类并不能达到你想要的效果.WPHotspotLabel是使用了CoreText的自动布局来检测属性点击的区域,这个也许会在不同的布局当中显示异常.目前所进行的所有测试当中,包括了文本的格式相关的都没有什么问题.当点击事件区域的检测也许会出现异常,这里已经给你提醒了哦!^_^
Note that these classes have not been tested as exhaustively, so it is possible that these do not behave as expected under all conditions. In particular, WPHotspotLabel uses CoreText layout to detect the attributes of the tapped position, which could potentially result in different layout than the one being displayed. Under all tests performed so far with simple labels and formatting, the detection does work correctly, but  you have been warned!

Example
--

![Screen](screen.png)


    // Example using fonts and colours
    NSDictionary* style1 = @{@"body":[UIFont fontWithName:@"HelveticaNeue" size:18.0],
                             @"bold":[UIFont fontWithName:@"HelveticaNeue-Bold" size:18.0],
                             @"red": [UIColor redColor]};


 

     // Example using arrays of styles, dictionary attributes for underlining and image styles

     NSDictionary* style2 = @{@"body" :
                                 @[[UIFont fontWithName:@"HelveticaNeue-Bold" size:18.0],
                                   [UIColor darkGrayColor]],
                                @"u": @[[UIColor blueColor],
                                    @{NSUnderlineStyleAttributeName : @(kCTUnderlineStyleSingle|kCTUnderlinePatternSolid)}
                                     ],
                                @"thumb":[UIImage imageNamed:@"thumbIcon"] };


    // Example using blocks for actions when text is tapped. Uses the 'link' attribute to style the links

    NSDictionary* style3 = @{@"body":[UIFont fontWithName:@"HelveticaNeue" size:22.0],
                             @"help":[WPAttributedStyleAction styledActionWithAction:^{
                                 NSLog(@"Help action");
                             }],
                             @"settings":[WPAttributedStyleAction styledActionWithAction:^{
                                 NSLog(@"Settings action");
                             }],
                             @"link": [UIColor orangeColor]};

    self.label1.attributedText = [@"Attributed <bold>Bold</bold> <red>Red</red> text" attributedStringWithStyleBook:style1];

    self.label2.attributedText = [@"<thumb> </thumb> Multiple <u>styles</u> text <thumb> </thumb>" attributedStringWithStyleBook:style2];

    self.label3.attributedText = [@"Tap <help>here</help> to show help or <settings>here</settings> to show settings" attributedStringWithStyleBook:style3];


How it works
--

The utility consists of two categories:

<b>NSMutableString+TagReplace</b> : Used internally to strip all tags out of a NSMutableString, building an array of tags with start and end ranges.

<b>NSString+WPAttributedMarkup</b> : Contains a single public method:

    -(NSAttributedString*)attributedStringWithStyleBook:(NSDictionary*)styleBook;

This builds an arrange of tags using NSMutableString+TagReplace, then iterates through each of the tags and applies the styles found in the style book.

If no style is found for a tag, then the tag is simply stripped from the string with no style applied.

If the <b>body</b> tag is found in the style book, then this is applied to the entire string before any other styles are applied.








 