# ExtractEmbeddedSources

.NET アセンブリのポータブル PDB に埋め込まれたソースコードを抽出するツールです。

## 概要

ExtractEmbeddedSources は、.NET アセンブリ（DLL）やポータブル PDB ファイルから埋め込まれたソースコードを抽出し、ファイルとして保存するコマンドラインツールです。デバッグ情報に含まれる EmbeddedSource を解析し、元のディレクトリ構造を維持しながらソースファイルを復元します。

## 主な機能

- DLL ファイルに埋め込まれたポータブル PDB からソースコードを抽出
- スタンドアロンのポータブル PDB ファイルからソースコードを抽出
- 圧縮されたソースコードの自動展開
- 元のディレクトリ構造を維持した出力
- ディレクトリ内の複数ファイルの一括処理

## システム要件

- .NET 7.0 以降

## インストール

1. リポジトリをクローンします：
```bash
git clone https://github.com/matsuaki/ExtractEmbeddedSources.git
cd ExtractEmbeddedSources
```

2. プロジェクトをビルドします：
```bash
dotnet build -c Release
```

## 使用方法

### 基本的な使い方

```bash
ExtractEmbeddedSources.exe [-o=<出力ディレクトリ>] <入力ファイルまたはディレクトリ>
```

### パラメータ

- `<入力ファイルまたはディレクトリ>`: 処理対象の DLL/PDB ファイルまたはディレクトリ（必須）
- `-o, --output`: 抽出したソースファイルの出力先ディレクトリ（省略時は現在のディレクトリ）
- `--recursive`: ディレクトリ指定時にサブディレクトリも再帰的に処理
- `--exclude`: 除外するパスパターン（カンマまたはセミコロン区切り）

### 使用例

#### 単一ファイルの処理
```bash
# DLL ファイルから抽出
ExtractEmbeddedSources.exe MyApp.dll

# PDB ファイルから抽出
ExtractEmbeddedSources.exe MyApp.pdb

# 出力先を指定
ExtractEmbeddedSources.exe -o=./extracted MyApp.dll
```

#### ディレクトリの処理
```bash
# ディレクトリ内のすべての DLL/PDB を処理
ExtractEmbeddedSources.exe ./bin/Debug

# サブディレクトリも含めて処理
ExtractEmbeddedSources.exe --recursive ./bin

# 特定のパスを除外
ExtractEmbeddedSources.exe --exclude=test,temp ./bin
```

## 動作原理

1. **ファイル形式の判定**: 入力ファイルの拡張子（.dll または .pdb）を確認
2. **メタデータの読み込み**: 
   - DLL の場合：埋め込まれたポータブル PDB を検索
   - PDB の場合：直接メタデータを読み込み
3. **ソースコードの抽出**: CustomDebugInformation から EmbeddedSource を特定
4. **圧縮の処理**: 圧縮されている場合は DeflateStream で展開
5. **ファイル出力**: 元のパス構造を維持しながらファイルとして保存

## 技術詳細

### 対応フォーマット
- ポータブル PDB（Portable Program Database）
- 埋め込みポータブル PDB を含む .NET アセンブリ

## 制限事項

- ポータブル PDB 形式のみ対応（従来の Windows PDB は非対応）
- EmbeddedSource が含まれていない場合は抽出できません
- 出力ファイルのパスが無効な場合はスキップされます

## ビルド方法

```bash
# デバッグビルド
dotnet build

# リリースビルド
dotnet build -c Release

# 実行可能ファイルの公開
dotnet publish -c Release -r win-x64 --self-contained
```

## ライセンス

このプロジェクトは MIT ライセンスの下で公開されています。詳細は [LICENSE](LICENSE) ファイルを参照してください。

## 作者

Copyright (c) 2023 matsuaki

## 参考リンク

- [Roslyn - PortableCustomDebugInfoKinds.cs](https://github.com/dotnet/roslyn/blob/main/src/Dependencies/CodeAnalysis.Debugging/PortableCustomDebugInfoKinds.cs)
- [.NET Portable PDB Documentation](https://github.com/dotnet/runtime/blob/main/docs/design/specs/PortablePdb-Metadata.md)
