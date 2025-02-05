# Event Destinations Specification

Status: <span style="color:red;font-weight:bolder;">DRAFT</span>

## 1. Introduction

This document outlines the specification for implementing event destinations. This specification aims to provide a comprehensive guide for developers to implement event destinations that meet industry standards and best practices. By following this specification, developers can ensure that their event destinations implementations are robust, flexible, and capable of handling diverse event delivery scenarios.

## 2. What are Event Destinations

Event Destinations expand the capabilities of event producers, such as API platforms, and benefit event consumers by providing more efficient and reliable event delivery options beyond just traditional webhooks.

Event Destinations are endpoints or systems to which event producers can send events and give the developer the choice to use the tools they are familiar with directly. For example:

- Google Cloud Pub/Sub
- AWS SQS
- Hookdeck Event Gateway
- Amazon EventBridge
- Kafka
- RabbitMQ
- HTTP webhooks

## 3. Goals

The goals of this specification are:

1. Define standard guidelines for implementing event destinations that ensures reliability, efficiency, and flexibility.
2. Provide clear guidelines for required and recommended features to support various event delivery scenarios.
3. Ensure compatibility with popular event destinations and integration patterns.
4. Promote best practices for event delivery, including retries, failure handling, and delivery guarantees.

## 4. Specification

This section outlines the required and recommended features for implementing event destinations. The goal is to provide a robust and flexible implementation that can handle various event delivery scenarios efficiently and reliably.

### 4.1 Required Features

These features are essential for any event destination implementation to ensure basic functionality and reliability.

#### 4.1.1 Support least two event destination types, including webhooks

The implementation must support at least two types of event destinations. One of these must be HTTP webhooks, given their widespread use and simplicity. The second type can be chosen from other popular event destinations such as message queues or event buses. This requirement ensures that the implementation can cater to different use cases, integrations needs, and preferences of developers.

Examples of supported event destination types include:

- HTTP webhooks
- Message queues (e.g., AWS SQS, RabbitMQ)
- Event buses (e.g., Amazon EventBridge, Google Cloud Pub/Sub)

#### 4.1.2 Automatic delivery retries with exponential backoff

The implementation must include automatic delivery retries with an exponential backoff strategy. This means that if an event delivery attempt fails, the implementation will wait for a progressively longer period before retrying, up to a maximum number of retries or a maximum wait time. This approach helps to prevent overwhelming the destination with repeated requests in a short period and increases the likelihood of successful delivery over time.

Key aspects of this feature include:

- **Configurable retry limits**: Allow developers to set the maximum number of retry attempts and the maximum wait time between retries.
- **Exponential backoff algorithm**: Implement an exponential backoff algorithm to calculate the wait time between retries, typically doubling the wait time after each failed attempt.
- **Error handling**: Properly handle different types of errors (e.g., network issues, server errors) and determine whether a retry is appropriate based on the error type.
- **Logging and monitoring**: Provide logging and monitoring capabilities to track retry attempts, successes, and failures, enabling developers to diagnose and address issues effectively.

By incorporating automatic delivery retries with exponential backoff, the event destination implementation can achieve higher reliability and resilience in the face of transient failures.

#### 4.1.3 APIs to create, update and delete destinations

The implementation must provide APIs to manage event destinations, including creating, updating, and deleting destinations.

Key aspects of this feature include:

- **Create API**: An endpoint to create a new event destination. The API should accept necessary parameters such as destination type, destinations URI, authentication details, and any other required configuration settings.
- **Update API**: An endpoint to update an existing event destination. The API should allow modifications to the destination's configuration, such as changing the destination URI, updating authentication details, or modifying other settings.
- **Delete API**: An endpoint to delete an existing event destination. The API should ensure that the destination is properly removed and that no further events are sent to it.

By providing these APIs, the implementation allows developers to manage their event destinations efficiently and integrate the management of these destinations into their existing workflows and automation processes.

#### 4.1.4 Alerts for destination failures

The implementation must include alerting mechanisms to notify developers of destination failures. The implementation must support at least one notification channel.

Considerations for feature include:

- **Configurable alert thresholds**: Allow developers to set thresholds for triggering alerts, such as a specific number of consecutive failures or a percentage of failed attempts within a given time frame.
- **Notification channels**: Support multiple notification channels, such as email, SMS, Slack, or other messaging platforms, to ensure that alerts reach the appropriate personnel.
- **Detailed alert information**: Provide detailed information in the alerts, including the destination that failed, the error encountered, and any relevant context or logs to help diagnose the issue.
- **Integration with monitoring tools**: Enable integration with popular monitoring and incident management tools, such as Prometheus, Grafana, or PagerDuty, to streamline the alerting and resolution process.

By incorporating alerts for destination failures, the event destination implementation can help developers maintain high reliability and quickly respond to issues that may affect event delivery.

#### 4.2 Recommended Features

These features are not mandatory but are highly recommended to enhance the overall performance, reliability, and developer experience of the event destination implementation.

#### 4.2.1 At-least-once event delivery guarantee

The implementation should provide an at-least-once event delivery guarantee to ensure that events are delivered to the destination at least once, even in the face of transient failures. This means that the system will make multiple attempts to deliver an event until it receives an acknowledgment from the destination, ensuring that no events are lost.

Key aspects of this feature include:

- **Acknowledgment mechanism**: Implement a mechanism for the destination to acknowledge the receipt of an event. This could be an HTTP status code, a message queue acknowledgment, or another form of confirmation.
- **Idempotency**: Ensure that the event delivery process is idempotent, meaning that multiple deliveries of the same event do not result in duplicate processing. This can be achieved by including unique event identifiers and having the destination handle duplicates appropriately.
- **Persistence**: Store events persistently until they are successfully delivered and acknowledged by the destination. This ensures that events are not lost in case of system crashes or restarts.
- **Monitoring and logging**: Provide monitoring and logging capabilities to track the delivery attempts and acknowledgments, enabling developers to diagnose and address any issues that may arise.

By implementing an at-least-once event delivery guarantee, the event destination implementation can provide higher reliability and ensure that all events are delivered, even in the presence of temporary failures.

#### 4.2.2 Event topics and topics-based subscriptions

The implementation should support event topics and topic-based subscriptions to allow developers to organize and filter events. This feature enables developers to subscribe to specific topics of interest and receive only the events relevant to those topics.

Functionality includes:

- **Subscription management**: Provide APIs to create, update, and delete subscriptions to specific topics. Developers should be able to subscribe to multiple topics and manage their subscriptions.
- **Event publishing**: Allow event producers to publish events to specific topics. The implementation should ensure that events are delivered only to the subscribers of the relevant topics.
- **Filtering and routing**: Implement filtering and routing mechanisms to ensure that events are delivered to the appropriate subscribers based on their topic subscriptions.
- **Monitoring and logging**: Provide monitoring and logging capabilities to track topic creation, subscription management, and event delivery, enabling developers to diagnose and address any issues that may arise.

By supporting event topics and topic-based subscriptions, the event destination implementation can offer greater flexibility and efficiency in event delivery, allowing developers to tailor their event consumption to their specific needs.

#### 4.2.3 Auto-disabling of failing destinations after too many failures

The implementation should automatically disable event destinations that experience a high number of consecutive failures. This feature helps to prevent continuous attempts to deliver events to destinations that are consistently failing, which can save resources and reduce the risk of overwhelming the destination.

Key aspects of this feature include:

- **Failure threshold**: Allow developers to configure the number of consecutive failures that will trigger the auto-disabling of a destination.
- **Notification**: Notify developers when a destination is auto-disabled due to excessive failures. This notification should include detailed information about the destination and the errors encountered, similar to the alerts described in section 4.1.4.
- **Re-enabling**: Provide mechanisms for developers to manually re-enable a disabled destination once the issue has been resolved. This can be done through an API or a user interface.
- **Logging and monitoring**: Track and log the auto-disabling events, including the reasons for disabling and any subsequent actions taken by developers. This information can help diagnose and address the underlying issues causing the failures.

By incorporating auto-disabling of failing destinations, the event destination implementation can improve overall system stability and ensure that resources are used efficiently.

#### 4.2.4 Developer-facing UI to configure destinations & inspect events

The implementation should provide a user-friendly interface (UX) for developers to configure event destinations and inspect events. This feature enhances the developer experience by making it easier to manage event destinations and troubleshoot issues.

Key aspects of this feature include:

- **Configuration UI**: A web-based interface that allows developers to create, update, and delete event destinations. The UI should provide forms and validation to ensure that all necessary information is collected and correctly formatted.
- **Event inspection**: A tool within the UI that allows developers to view and search through events that have been sent to destinations. This tool should display event details, including payload, headers, and delivery status.
- **Filtering and sorting**: Enable developers to filter and sort events based on various criteria, such as destination, status, timestamp, and payload content. This helps developers quickly find relevant events and diagnose issues.
- **Error details**: Provide detailed error information for failed events, including error messages, stack traces, and any relevant logs. This information can help developers understand why an event delivery failed and how to fix the issue.
- **Integration with APIs**: Ensure that all actions available in the UI can also be performed via APIs, allowing developers to automate the management of event destinations and event inspection.

By offering a developer-facing UX for configuring destinations and inspecting events, the event destination implementation can improve usability and streamline the process of managing and troubleshooting event deliveries.

#### 4.2.5 Manual retries via UI or API

The implementation should allow developers to manually retry event deliveries via the user interface (UI) or an API. This feature provides flexibility for developers to address and resolve issues with event delivery on a case-by-case basis.

Key aspects of this feature include:

- **Retry via UI**: Provide a user-friendly interface that allows developers to select one or more failed events and trigger a manual retry. The UI should display relevant information about the events, such as payload, headers, and error details, to help developers decide which events to retry.
- **Retry via API**: Offer an API endpoint that enables developers to programmatically trigger retries for specific events. This allows for automation and integration with other systems or workflows.
- **Retry limits**: Implement configurable limits on the number of manual retries to prevent abuse or excessive resource consumption. Developers should be able to set these limits based on their requirements.
- **Logging and monitoring**: Track and log all manual retry attempts, including the user or system that initiated the retry, the events retried, and the outcomes. This information can help diagnose issues and improve the reliability of event delivery.

By providing manual retries via UI or API, the event destination implementation can offer greater control and flexibility for developers to ensure successful event delivery.

#### 4.2.6 Filtering based on payload content

The implementation should support filtering events based on their payload content. This feature allows developers to define rules that determine which events should be delivered to specific destinations based on the content of the event payload.

Key aspects of this feature include:

- **Rule definition**: Provide a mechanism for developers to define filtering rules based on payload content. These rules can be based on specific fields, values, or patterns within the payload.
- **Flexible conditions**: Support various conditions for filtering, such as equality, inequality, contains, starts with, ends with, and regular expressions. This flexibility allows developers to create precise and complex filtering criteria.
- **Configuration UI**: Offer a user-friendly interface for creating and managing filtering rules. The UI should allow developers to define and modify rules without requiring deep technical knowledge.
- **API support**: Ensure that filtering rules can also be managed via APIs, enabling automation and integration with other systems or workflows.
- **Performance considerations**: Implement efficient filtering mechanisms to ensure that the filtering process does not negatively impact the performance of event delivery.
- **Logging and monitoring**: Provide logging and monitoring capabilities to track the application of filtering rules, including which events were filtered out and which were delivered. This information can help developers understand the impact of their filtering rules and diagnose any issues.

By supporting filtering based on payload content, the event destination implementation can offer greater control and customization for event delivery, allowing developers to ensure that only relevant events are sent to their destinations.

## Feedback

See [CONTRIBUTING](CONTRIBUTING.md) for more details on providing feedback and suggesting corrections and improvements.
