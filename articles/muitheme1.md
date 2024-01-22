---
title: "MUIでテーマを設定する"
emoji: "⛳"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["mui", "style", "css", "react"]
published: true
---

## MUI でのテーマの設定方法

コンポーネントの色や文字サイズ、breakpoint などを変更できます。

```tsx
const theme = createTheme({
  primary: {
    main: "#123456",
  },
});

<ThemeProvider theme={theme}>{/* Components */}</ThemeProvider>;
```

新しい変数を追加するには、type の設定が必要です。

```ts
declare module "@mui/material/styles" {
  interface Theme {
    status: {
      danger: string;
    };
  }
  interface ZIndex {
    globalHeader: number;
  }
}
```

### テーマ設定の注意点

#### 範囲

コンポーネント単体に`ThemeProvider`を適用し、部分的にテーマを設定することが可能です。
`ThemeProvider`でラップしたコンポーネントを、別のテーマを設定した`ThemeProvider`でラップした時、外側のテーマはコンポーネントには適用されません。
テーマを設定しているコンポーネント内から外側のテーマにアクセスするときは、`useTheme` を使う必要があります。

```tsx
import { createTheme, ThemeProvider, useTheme } from "@mui/material/styles";

function Child() {
  const outerTheme = useTheme();
  const theme = createTheme({
    ...outerTheme,
    palette: { status: { danger: "#654321" } },
  });
  return (
    <ThemeProvider theme={theme}>
      <span>{`spacing ${theme.spacing}`}</span>
    </ThemeProvider>
  );
}
```

### MUI のスタイルの優先度

スタイルの優先度は、一般的に CSS のカスケードと同様に、特定性と宣言の順序に基づいています。ただし、MUI では以下の優先度が適用されます（高い順）。
テーマは上書き可能です。

1. Inline sx prop: このプロップはインラインスタイルとして適用されるため、他のほとんどの方法よりも優先されます。
1. className with CSS-in-JS: スタイルが特定のコンポーネントに直接適用されるため、非常に特定的です。
1. Global theme customization: テーマによるスタイルは、デフォルトのスタイルよりも優先されますが、コンポーネントに直接適用される className や sx prop には劣ります。
1. MUI default styles: MUI に組み込まれているデフォルトのスタイルが最も低い優先度です。
