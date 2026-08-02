# VC/VP Employee Badge

**VC/VP Employee Badge** は、Verifiable Credentials（VC）とVerifiable Presentations（VP）の考え方を参考に、本人承認と項目単位の情報開示を取り入れた独自の社員証システムです。

このプロジェクトはW3C VC Data ModelやOpenID4VPに準拠した実装ではありません。小規模な企業で利用する社員証として必要な体験と安全性を整理し、Firebase、Google Workspace、Apple Wallet、Google Walletなどを組み合わせて実装しています。

## プロジェクトの目的

一般的な社員証は、提示した時点で氏名、顔写真、社員番号、所属などがまとめて見えることがあります。本プロジェクトでは、社員証を持っていることの確認と、追加情報の開示を分けます。

主な目的は次のとおりです。

- 社員、会社、検証者の役割を分ける
- 検証時に社員本人の承認を求める
- 必要な情報だけを項目単位で開示する
- QRコードのコピーや再利用によるなりすましを抑える
- 退職や紛失時に社員証を即時失効できるようにする
- 発行、失効、検証、承認の履歴を監査できるようにする

## VC/VPから参考にした考え方

VC/VPでは、資格情報を発行するIssuer、資格情報を保持するHolder、提示された情報を確認するVerifierという役割が整理されています。

本プロジェクトでも、会社を発行側、社員を保持者に相当する役割、受付や確認担当者を検証者として捉えました。また、本人の同意、最小限の情報開示、短時間だけ有効な提示、失効という考え方を取り入れています。

一方で、W3C形式のVCを発行して社員のWalletへ渡したり、社員側でVPを生成したりする仕組みは実装していません。VPは、提示の改ざんを検知でき、その提示をHolder本人が作成したことを暗号学的に確認できる形式ですが、本プロジェクトではその役割を本人承認の仕組みで代替しています。

また、識別子や公開鍵、失効情報を参照するVerifiable Data Registryに相当する部分も、会社のバックエンドに集約しています。検証は会社のバックエンドを信頼点とするオンライン方式です。

## 実装している機能

- Google Workspaceアカウントを利用した社員認証
- Web上での社員証表示
- Apple WalletとGoogle Walletへの社員証追加
- 5分間有効な署名付き動的QRコード
- 使用済みQRコードの記録によるリプレイ攻撃対策
- 検証要求に対する社員本人の承認・拒否
- 社員番号、顔写真、連絡先などの項目単位の開示選択
- 承認後15分間に限定した情報表示
- 社員証の失効・再発行
- 発行、失効、検証に関する監査ログ

## 現在の状況

社員証の発行、Walletへの追加、動的QRコードによる検証、本人承認、情報開示、失効、監査まで、一連の機能を実装しています。

標準準拠や他社システムとの相互運用性を目的とするのではなく、小規模な企業で現実的に運用できる独自の社員証として開発を続けています。

## 関連リンク

- [VC/VPの考え方を参考に、独自の社員証アプリを作りました](../blog/posts/2026-08-03-vc-vp-inspired-employee-badge.md)
- [W3C Verifiable Credentials Data Model 2.0](https://www.w3.org/TR/vc-data-model-2.0/)
- [OpenID for Verifiable Presentations 1.0](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html)
- [OpenID for Verifiable Credential Issuance 1.0](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html)
