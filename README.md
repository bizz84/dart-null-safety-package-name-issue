## Null Safety only works if package name is `test`

I've come across a strange issue where a Dart command line app only compiles code using Null Safety if the package name is `test` inside `pubspec.yaml`.

Here's a minimal project with reproducible steps (on my machine):

**pubspec.yaml**:

```yaml
# pubspec.yaml
name: test
description: A simple command-line application.
# version: 1.0.0
# homepage: https://www.example.com

environment:
  sdk: '>=2.10.0 <3.0.0'

dependencies:
  
dev_dependencies:
  pedantic: ^1.9.0
```

**analysis_options.yaml**:

```yaml
# Defines a default set of lint rules enforced for
# projects at Google. For details and rationale,
# see https://github.com/dart-lang/pedantic#enabled-lints.
include: package:pedantic/analysis_options.yaml

# For lint rules and documentation, see http://dart-lang.github.io/linter/lints.
# Uncomment to specify additional rules.
# linter:
#   rules:
#     - camel_case_types

analyzer:
  enable-experiment:
    - non-nullable
```

**main.dart**:

```dart
void foo({required int bar}) {}

void main() {}
```

If I run `dart main.dart`, the output is:

```
hello world
```

But if I change the package name to `test2` (or anything other than `test`) in `pubspec.yaml`, I get:

```
main.dart:1:24: Error: Expected '}' before this.
void foo({required int bar}) {}
                       ^^^
main.dart:1:11: Error: Type 'required' not found.
void foo({required int bar}) {}
          ^^^^^^^^
main.dart:1:11: Error: 'required' isn't a type.
void foo({required int bar}) {}
```

My `dart --version` is:

```
Dart SDK version: 2.10.0 (stable) (Mon Sep 28 09:21:23 2020 +0200) on "macos_x64"
```

I though this may have been an issue with my pub cache, so I tried `pub cache repair`, but to no avail.

Any ideas?
