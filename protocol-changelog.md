# Protocol Change Log

## 3.4.0 (11/27/2017)

* [extensible completion item and symbol kinds](https://github.com/Microsoft/language-server-protocol/issues/129)

## 3.3.0 (11/24/2017)

* Added support for `CompletionContext`
* Added support for `MarkupContent`
* Removed old New and Updated markers.

## 3.2.0 (09/26/2017)

* Added optional `commitCharacters` property to the `CompletionItem`

## 3.1.0 (02/28/2017)

* Make the `WorkspaceEdit` changes backwards compatible.
* Updated the specification to correctly describe the breaking changes from 2.x to 3.x around `WorkspaceEdit`and `TextDocumentEdit`.

## 3.0 Version

- add support for client feature flags to support that servers can adapt to different client capabilities. An example is the new `textDocument/willSaveWaitUntil` request which not all clients might be able to support. If the feature is disabled in the client capabilities sent on the initialize request, the server can't rely on receiving the request.
- add support to experiment with new features. The new `ClientCapabilities.experimental` section together with feature flags allow servers to provide experimental feature without the need of ALL clients to adopt them immediatelly.
- servers can more dynamically react to client features. Capabilities can now be registered and unregistered after the initialize request using the new `client/registerCapability` and `client/unregisterCapability`. This for example allows servers to react to settings or configuration changes without a restart.
- add support for `textDocument/willSave` notification and `textDocument/willSaveWaitUntil` request.
- add support for `textDocument/documentLink` request.
- add a `rootUri` property to the initializeParams in favour of the `rootPath` property.