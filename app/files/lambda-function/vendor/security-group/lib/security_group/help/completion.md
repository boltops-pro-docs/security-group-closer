<!-- note marker start -->
**NOTE**: This repo contains only the documentation for the private BoltsOps Pro repo code.
Original file: https://github.com/boltopspro/security-group-closer/blob/master/app/files/lambda-function/vendor/security-group/lib/security_group/help/completion.md
The docs are publish so they are available for interested customers.
For access to the source code, you must be a paying BoltOps Pro subscriber.
If are interested, you can contact us at contact@boltops.com or https://www.boltops.com

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
