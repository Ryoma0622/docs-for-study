# Github Actions についてちょっと調べてみた
#Github #CI-CD/GHA

## What is Github Actions (GHA) ??
Github が開発した CI / CD ツール。
Circle CI や Drone CI と同じ感じ。
Git push や PR などのイベントをトリガーとして yml に定義した処理を実行することができる。

## Comparison with CircleCI
### CircleCI
* **Pros**
	* **Steps を再利用できる**
	* **Job をパラレルで実行できる**
	* ドキュメントやコミュニティのブログが多い
* **Cons**
	* 料金が高い

### Github Actions
* **Pros**
	* 他のダッシュボードを開かずとも CI を確認できる
	* Marketplace に [定義済みの Actions](https://github.com/marketplace?type=actions) が用意されている
	* 自分のランナーをホストできる [GithubDocs - 自己ホストランナーについて](https://docs.github.com/ja/free-pro-team@latest/actions/hosting-your-own-runners)
		* 柔軟なテストを実行できる
> [引用: GithubDocs - 自己ホストランナーについて](https://docs.github.com/ja/free-pro-team@latest/actions/hosting-your-own-runners/about-self-hosted-runners#%E8%87%AA%E5%B7%B1%E3%83%9B%E3%82%B9%E3%83%88%E3%83%A9%E3%83%B3%E3%83%8A%E3%83%BC%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)   
> セルフホストランナーでは、ハードウェア、オペレーティングシステム、ソフトウェアツールについてGitHubホストランナーよりもコントロールできます。 セルフホストランナーでは、大規模なジョブを実行するために処理能力やメモリを強化したカスタムハードウェア構成を作ったり、ローカルネットワークで利用できるソフトウェアをインストールしたり、GitHubホストランナーでは提供されていないオペレーティングシステムを選択したりできます。 **セルフホストランナーは、物理、仮想、コンテナ内、オンプレミス、クラウド内のいずれでも可能です。**  

* **Cons**
	* **Steps を使い回せない**
	* **Job をパラレルで実行できない**

## How to use
### commit message の内容で skip
https://ryangjchandler.co.uk/articles/running-github-actions-for-certain-commit-messages

```yaml
jobs:
  format:
    runs-on: ubuntu-latest
    if: "! contains(github.event.head_commit.message, 'wip')"
```

`wip` が message に含まれる commit の場合 job を skip する。

### Github Pages へ Auto Deploy
https://github.blog/2020-09-25-github-action-hero-james-ives-and-github-pages-deploy/

## References
* https://blogs.vmware.com/opensource/2020/04/02/ci-tests-tools/
* https://techblog.code.or.jp/entry/2020/04/14/183000
* https://qiita.com/bigwheel/items/2ab7deb237122db2fb8d
* https://qiita.com/reireias/items/04b167fed442bca05de1