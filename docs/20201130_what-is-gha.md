# Github Actions についてちょっと調べてみた

#Github #CI-CD/GHA

## What is Github Actions??

Github が開発した CI / CD ツール。GHA と略される。
Circle CI や Drone CI と同じ感じ。
Git push や PR などのイベントをトリガーとして yml に定義した処理を実行することができる。

## Comparison with CircleCI

### CircleCI

* **Pros**
	* **Steps を再利用できる**
	* ドキュメントやコミュニティのブログが多い
* **Cons**
	* 料金が高い
		* [CIツール コスト比較 GithubActions,TravisCI,CircleCI](https://chariosan.com/2020/07/19/comparison_ci_tool_cost/#GithubActions)
			* > CircleCIはクレジット料金の他に、プロジェクトに関わるユーザー数に応じて課金されるようになっています。
			* 開発メンバーが増えていくにつれ、料金が上がる
		* [プログルのCI/CDをCircleCIからGithub Actionsに移行した話](https://techblog.code.or.jp/entry/2020/04/14/183000)
	
#### Example

```yaml
version: 2.1

# Define the jobs we want to run for this project
jobs:
  build:
    docker:
      - image: circleci/<language>:<version TAG>
        auth:
          username: mydockerhub-user
          password: $DOCKERHUB_PASSWORD  # context / project UI env-var reference
    steps:
      - checkout
      - run: echo "this is the build job"
  test:
    docker:
      - image: circleci/<language>:<version TAG>
        auth:
          username: mydockerhub-user
          password: $DOCKERHUB_PASSWORD  # context / project UI env-var reference
    steps:
      - checkout
      - run: echo "this is the test job"

# Orchestrate our job run sequence
workflows:
  build_and_test:
    jobs:
      - build
      - test
```


### Github Actions

* **Pros**
	* Githubとの高い統合性
		* 他のダッシュボードを開かずとも CI を確認できる
	* Marketplace に [定義済みの Actions](https://github.com/marketplace?type=actions) が用意されている
	* ディレクトリ単位で CI の実行をハンドリングできる
		* [on.<push|pull_request>.paths](https://docs.github.com/ja/free-pro-team@latest/actions/reference/workflow-syntax-for-github-actions#onpushpull_requestpaths) 
	* 自分のランナーをホストできる [GithubDocs - 自己ホストランナーについて](https://docs.github.com/ja/free-pro-team@latest/actions/hosting-your-own-runners)
		* 柔軟なテストを実行できる
> [引用: GithubDocs - 自己ホストランナーについて](https://docs.github.com/ja/free-pro-team@latest/actions/hosting-your-own-runners/about-self-hosted-runners#%E8%87%AA%E5%B7%B1%E3%83%9B%E3%82%B9%E3%83%88%E3%83%A9%E3%83%B3%E3%83%8A%E3%83%BC%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)   
> セルフホストランナーでは、ハードウェア、オペレーティングシステム、ソフトウェアツールについてGitHubホストランナーよりもコントロールできます。  
> セルフホストランナーでは、大規模なジョブを実行するために処理能力やメモリを強化したカスタムハードウェア構成を作ったり、ローカルネットワークで  
> 利用できるソフトウェアをインストールしたり、GitHubホストランナーでは提供されていないオペレーティングシステムを選択したりできます。  
> **セルフホストランナーは、物理、仮想、コンテナ内、オンプレミス、クラウド内のいずれでも可能です。**  

* **Cons**
	* **Steps を使い回せない**
	* **pushするブランチごとに  ファイルを作成する必要がある**
		* CircleCI の設定ファイルの workflows のようなキーがない為

#### Example

```yaml
# This is a basic workflow to help you get started with Actions
name: My First Action
# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the master branch
on:
  push:
    branches: [ dev, test, master ]
  pull_request:
    branches: [ dev, test, master ]
# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - uses: actions/checkout@v2
    # Runs a single command using the runners shell
    - name: Run a one-line script
      run: echo Hello, world!
    # Runs a set of commands using the runners shell
    - name: Run a multi-line script
      run: |
        echo Add other actions to build,
        echo test, and deploy your project.
```

## Tips

### commit message の内容で skip

[Running GitHub Actions for Certain Commit Messages](https://ryangjchandler.co.uk/articles/running-github-actions-for-certain-commit-messages)

_i.e ‘wip’ が message に含まれる commit の場合 job を skip する_

```yaml
jobs:
  format:
    runs-on: ubuntu-latest
    if: "! contains(github.event.head_commit.message, 'wip')"
```

### Github Pages へ Auto Deploy

[GitHub Action Hero · James Ives and “GitHub Pages Deploy”](https://github.blog/2020-09-25-github-action-hero-james-ives-and-github-pages-deploy/)
[GitHub - JamesIves/github-pages-deploy-action: GitHub action for deploying a project to GitHub pages.](https://github.com/JamesIves/github-pages-deploy-action)

## References

* [Comparing GitHub Actions and CircleCI for Testing Pull Request Changes](https://blogs.vmware.com/opensource/2020/04/02/ci-tests-tools/)
* [プログルのCI/CDをCircleCIからGithub Actionsに移行した話](https://techblog.code.or.jp/entry/2020/04/14/183000) 
* [Github Actions 8ヶ月使ってみてわかったことまとめ](https://qiita.com/bigwheel/items/2ab7deb237122db2fb8d)
* [GitHub Actions、Travis CI、CircleCIの気になる部分の比較](https://qiita.com/reireias/items/04b167fed442bca05de1)
* 

