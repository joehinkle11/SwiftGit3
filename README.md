# SwiftGit3, as used in [App Maker Professional](https://appmakerios.com)

Swift bindings to [libgit2](https://github.com/libgit2/libgit2).

This version supports SSH cloning, changing branches, git push/pull, and works well with [GitProviders](https://github.com/App-Maker-Software/GitProviders), a SPM which provides a SwiftUI interface for managing git hosting provider credentials.

All binary dependencies are now served as local XCFrameworks included in this repository.

SwiftGit3 is a fork of [SwiftGit2](http://github.com/SwiftGit2/SwiftGit2), and tries to follow their lead as close as possible. The name `SwiftGit3` is only used in the repository name, so the Swift Package, tests, files, etc. will all use the `SwiftGit2` naming.

```swift
import SwiftGit2

let URL: URL = ...
let result = Repository.at(URL)
switch result {
case let .success(repo):
    let latestCommit = repo
        .HEAD()
        .flatMap {
            repo.commit($0.oid)
        }

    switch latestCommit {
    case let .success(commit):
        print("Latest Commit: \(commit.message) by \(commit.author.name)")

    case let .failure(error):
        print("Could not get commit: \(error)")
    }

case let .failure(error):
    print("Could not open repository: \(error)")
}
```

## Adding SwiftGit2 to your Project

```swift
// swift-tools-version:4.0
import PackageDescription

let package = Package(
    name: "YOUR_PROJECT_NAME",
    dependencies: [
        .package(url: "https://github.com/App-Maker-Software/SwiftGit3.git", from: "1.2.0"),
    ]
)
```

## SwiftGit3 is a fork of [SwiftGit2](https://github.com/SwiftGit2/SwiftGit2/)

The difference is that all major improvments which have been stuck in the PRs at SwiftGit2 are in the `main` branch here, and new features, such as branch changes, push, pull, SSH cloning, etc, are supported here. Because a real app in production is using SwiftGit3, it's subject to be updated frequently as new feature requests are complete.

There's lot of work needed to be done to clean up some of the new features. PRs are very welcome. While it would be unfortunate for SwiftGit3 to diverge far from SwiftGit2, it's better that problems here get fixed than waiting on activity from SwiftGit2.

## Design
SwiftGit3 uses value objects wherever possible. That means using Swift’s `struct`s and `enum`s without holding references to libgit2 objects. This has a number of advantages:

1. Values can be used concurrently.
2. Consuming values won’t result in disk access.
3. Disk access can be contained to a smaller number of APIs.

This vastly simplifies the design of long-lived applications, which are the most common use case with Swift. Consequently, SwiftGit3 APIs don’t necessarily map 1-to-1 with libgit2 APIs.

All methods for reading from or writing to a repository are on SwiftGit’s only `class`: `Repository`. This highlights the failability and mutation of these methods, while freeing up all other instances to be immutable `struct`s and `enum`s.

## Required Tools (for non-SPM builds)
To build SwiftGit2, you'll need the following tools installed locally:

* cmake
* libssh2
* libtool
* autoconf
* automake
* pkg-config

```
brew install cmake libssh2 libtool autoconf automake pkg-config
```

## Contributions
We :heart: to receive pull requests! GitHub makes it easy:

1. Fork the repository
2. Create a branch with your changes
3. Send a Pull Request

All contributions should match GitHub’s [Swift Style Guide](https://github.com/github/swift-style-guide).

## License
SwiftGit3 is available under the MIT license.
