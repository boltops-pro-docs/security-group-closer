<!-- note marker start -->
NOTE: This repo contains only the documentation for the private BoltsOps repo code.
Original file: https://github.com/boltops-pro/security-group-closer/blob/master/app/files/lambda-function/vendor/security-group/lib/security_group/help/completion.md
The docs are publish so they are available for interested subscribers.
For access to the source code, you can become a BoltOps subscriber.
See: https://learn.boltops.com

<!-- note marker end -->

## Examples

    security-group completion

Prints words for TAB auto-completion.

    security-group completion
    security-group completion hello
    security-group completion hello name

To enable, TAB auto-completion add the following to your profile:

    eval $(security-group completion_script)

Auto-completion example usage:

    security-group [TAB]
    security-group hello [TAB]
    security-group hello name [TAB]
    security-group hello name --[TAB]
