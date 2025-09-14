Let's consider a hypothetical indie game development team of 6 people,
"Makers' Tavern". The team is building a purpose-built game engine in house,
using the [Zig](https://ziglang.org) programming language and SDL.

## Zig

Zig is a compiled systems language that seems to have taken influence from C
and Rust. The Zig language's aim is to provide a whole toolbox for building
"robust, optimal and reusable software", so most if not all basic tools that
the development team needs to run in CI come directly from the Zig project,
bundled with the `zig` toolchain.

## Toolchain

The basic tools for linting, testing and building source files with `zig` are:
- Linting: `zig fmt --check file.zig`
- Testing: `zig test file.zig`
- Building: `zig build-exe file.zig` (or `build-lib`, `build-obj`)

However, these are just the plain tools, which aren't directly used for complex
projects with multiple files. Instead, the team is using the *Zig build system*!

## Build system

The Zig build system compiles a "build script" (named `build.zig` in the
project root), which generates a graph of build steps, that are then passed to
a runner which performs the build. The build system is invoked by `zig build`,
and by default it runs a step called `install`, which builds the main binaries,
possibly also other artifacts, and copies them under `zig-out`.

The default `install` step already explains how the team can build the game in
their CI pipeline, but what about linting and testing?

### Linting

As outlined in a [Ziggit post](https://ziggit.dev/t/build-system-tricks/3531),
the trick to running `zig fmt` from the build system is to add a
[fmt step](https://ziglang.org/documentation/master/std/#std.Build.addFmt)
in your `build.zig` -file. I tested this in a freshly initialized project, and
introduced a formatting error in `src/root.zig`:
```console
$ zig build fmt
fmt
└─ zig fmt --check failure
error: src/root.zig: non-conforming formatting
```

### Tests

The `build.zig` template already has testing set up for you when you initialize
a Zig project with `zig init`. Here's how it's set up by default:
```zig
const exe_tests = b.addTest(.{
    .root_module = exe.root_module,
});
const run_exe_tests = b.addRunArtifact(exe_tests);
const test_step = b.step("test", "Run tests");
test_step.dependOn(&run_exe_tests.step);
```

Basically, first we create a step that compiles `test` blocks from the project's
source module into a test runner executable, then define a run step that when
invoked, will run the test runner. Finally, we create a top-level step, like
`install`, but we name it `test`, and make it depend on the run step.

This way if we invoke `zig build test` -step, the output may look like this:
```console
$ zig build test --summary all

Build Summary: 5/5 steps succeeded; 3/3 tests passed
test success
├─ run test 1 passed 168us MaxRSS:2M
│  └─ compile test Debug native cached 3ms MaxRSS:38M
└─ run test 2 passed 172us MaxRSS:2M
   └─ compile test Debug native cached 4ms MaxRSS:38M
```

## CI alternatives

Some alternatives for setting up CI include CircleCI (cloud-based) and
GitLab (self-hosted and cloud-based options).

## Self-hosted CI

The "Makers' Tavern" team can probably run linting, builds and many unit tests
for the game engine's code and game logic with a cloud-based CI solution.
However, to test GPU support and hardware rendering, and to run accurate
benchmarks on known hardware, a self-hosted CI setup is necessary.

## LLM/AI statement

No LLMs or other AI tools were used for this exercise.
