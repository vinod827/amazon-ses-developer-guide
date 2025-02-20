# Set up a CloudWatch event destination for event publishing<a name="event-publishing-add-event-destination-cloudwatch"></a>

You can use Amazon CloudWatch event destinations to publish Amazon SES email sending events to CloudWatch\. Because a CloudWatch event destination exists within a configuration set only, you must first [create a configuration set](event-publishing-create-configuration-set.md) and then add the event destination to the configuration set\.

When you add a CloudWatch event destination to a configuration set, you must choose one or more CloudWatch *dimensions* that correspond to the message tags you use when you send your emails\. Like message tags, a CloudWatch dimension is a name/value pair that helps you uniquely identify a metric\.

For example, you might have a message tag and a dimension called `campaign` that you use to identify your email campaign\. When you publish your email sending events to CloudWatch, choosing your message tags and dimensions is important because these choices affect your CloudWatch billing and determine how you can filter your email sending event data in CloudWatch\.

This section provides information to help you choose your dimensions, and then shows how to add a CloudWatch event destination to a configuration set\.

**Topics**
+ [Adding a CloudWatch Event Destination](#event-publishing-add-event-destination-cloudwatch-add)
+ [Choosing CloudWatch Dimensions](#event-publishing-add-event-destination-cloudwatch-dimensions)

## Adding a CloudWatch Event Destination<a name="event-publishing-add-event-destination-cloudwatch-add"></a>

The procedure in this section shows how to add CloudWatch event destination details to a configuration set and assumes you have completed steps 1 through 4 in [Add an event destination \(console\)](event-destinations-manage.md#event-destination-add)\.

You can also use the [UpdateConfigurationSetEventDestination](https://docs.aws.amazon.com/ses/latest/APIReference-V2/API_UpdateConfigurationSetEventDestination.html) operation in the Amazon SES API V2 to create and modify event destinations\.

**To add CloudWatch event destination details to a configuration set using the console**

1. After selecting the CloudWatch **Destination type** and enabling **Event publishing** in [Step 4](event-destinations-manage.md#add-event-destination-step-4) of [Add an event destination \(console\)](event-destinations-manage.md#event-destination-add), the respective service's detail panel will appear\. The panel's fields are addressed below\.

1. For **Value Source**, specify how Amazon SES will obtain the data that it passes to CloudWatch\. The following value sources are available:
   + **Message Tag** – Amazon SES retrieves the dimension name and value from a tag that you specify by using the `X-SES-MESSAGE-TAGS` header or the `Tags` API parameter\. For more information about using message tags, see [Step 3: Specify your configuration set when you send email](event-publishing-send-email.md)\.
**Note**  
Message tags can include the numbers 0–9, the letters A–Z \(both uppercase and lowercase\), hyphens \(\-\), and underscores \(\_\)\.

     You can also use the **Message Tag** value source to create dimensions based on Amazon SES auto\-tags\. To use an auto\-tag, type the complete name of the auto\-tag as the **Dimension Name**\. For example, to create a dimension based on the configuration set auto\-tag, use `ses:configuration-set` for the **Dimension Name**, and the name of the configuration set for the **Default Value**\. For a complete list of auto\-tags, see [How event publishing works](monitor-using-event-publishing.md#event-publishing-how-works)\.
   + **Email Header** – Amazon SES retrieves the dimension name and value from a header in the email\.
**Note**  
You can't use any of the following email headers as the **Dimension Name**: `Received`, `To`, `From`, `DKIM-Signature`, `CC`, `message-id`, or `Return-Path`\.
   + **Link Tag** – Amazon SES retrieves the dimension name and value from a tag that you specified in a link\. For more information about adding tags to links, see [Can I tag links with unique identifiers?](faqs-metrics.md#sending-metric-faqs-clicks-q5)\.

1. For **Dimension Name**, type the name of the dimension that you want to pass to CloudWatch\.
**Note**  
Dimension names can contain only ASCII letters \(a\-z, A\-Z\), numbers \(0\-9\), underscores \(\_\), and dashes \(\-\)\. Spaces, accented characters, non\-Latin characters, and other special characters are not allowed\.

1. For **Default Value**, type the value of the dimension\.
**Note**  
Dimension values can contain only ASCII letters \(a\-z, A\-Z\), numbers \(0\-9\), underscores \(\_\), dashes \(\-\), at signs \(@\), and periods \(\.\)\. Spaces, accented characters, non\-Latin characters, and other special characters are not allowed\.

1. If you want to add more dimensions, choose **Add Dimension**\. Otherwise, choose **Next**\.

1. On the review screen, if you're satisfied with how you defined your event destination, choose **Add destination**\.

## Choosing CloudWatch Dimensions<a name="event-publishing-add-event-destination-cloudwatch-dimensions"></a>

When you choose names and values to use as CloudWatch dimensions, consider the following factors:
+ **Price per metric** – You can view basic Amazon SES metrics in CloudWatch for free\. However, when you collect metrics using event publishing, you create *custom metrics* in CloudWatch\. Each unique combination of event type, dimension name, and dimension value creates a different custom metric in CloudWatch\. When you use CloudWatch, you are charged for each custom metric you create\. For this reason, you might want to avoid choosing dimensions that can take many different values\. For example, unless you are very interested in tracking your email sending events by "From" domain, you might not want to define a dimension for the Amazon SES auto\-tag `ses:from-domain` because it can take many different values\. For more information, see [CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing)\. 
+ **Metric filtering** – If a metric has multiple dimensions, you cannot access the metric in CloudWatch based on each dimension separately\. For that reason, think carefully before you add more than one dimension to a single CloudWatch event destination\. For example, if you want metrics by `campaign` and by a combination of `campaign` and `genre`, you need to add two event destinations: one with only `campaign` as a dimension, and one with both `campaign` and `genre` as dimensions\.
+ **Dimension value source** – As an alternative to specifying your dimension values using Amazon SES\-specific headers or a parameter to the API, you can also choose for Amazon SES to take the dimension values from your own MIME message headers\. You might use this option if you are already using custom headers and you do not want to change your emails or your calls to the email sending API to collect metrics based on your header values\. If you use your own MIME message headers for Amazon SES event publishing, the header names and values that you use for Amazon SES event publishing may only include the letters A through Z, the numbers 0 through 9, underscores \(\_\), at signs \(@\), hyphens \(\-\), and periods \(\.\)\. If you specify a name or value that contains other characters, the email sending call will still succeed, but the event metrics will not be sent to Amazon CloudWatch\.

For more information about CloudWatch concepts, see [Amazon CloudWatch Concepts](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html) in the *Amazon CloudWatch User Guide*\.