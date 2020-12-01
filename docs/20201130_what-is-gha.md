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
		* 開発メンバーが増えていくにつれ、料金が上がる
			* [プログルのCI/CDをCircleCIからGithub Actionsに移行した話](https://techblog.code.or.jp/entry/2020/04/14/183000)
			* > CircleCIはクレジット料金の他に、プロジェクトに関わるユーザー数に応じて課金されるようになっています。 

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
		* [paths](https://docs.github.com/ja/free-pro-team@latest/actions/reference/workflow-syntax-for-github-actions#onpushpull_requestpaths)
	* 自分のランナーをホストできる [GithubDocs - 自己ホストランナーについて](https://docs.github.com/ja/free-pro-team@latest/actions/hosting-your-own-runners)
		* 柔軟なテストを実行できる
> セルフホストランナーでは、ハードウェア、オペレーティングシステム、ソフトウェアツールについてGitHubホストランナーよりもコントロールできます。  
> セルフホストランナーでは、大規模なジョブを実行するために処理能力やメモリを強化したカスタムハードウェア構成を作ったり、ローカルネットワークで  
> 利用できるソフトウェアをインストールしたり、GitHubホストランナーでは提供されていないオペレーティングシステムを選択したりできます。  
> **セルフホストランナーは、物理、仮想、コンテナ内、オンプレミス、クラウド内のいずれでも可能です。**  

* **Cons**
	* **Steps を使い回せない**
	* **pushするブランチごとに  ファイルを作成する必要がある**
		* CircleCI の設定ファイルの [workflows](https://circleci.com/docs/ja/2.0/configuration-reference/#workflows) キーのような記法がない為

#### Example

```yaml
# This is a basic workflow to help you get started with Actions
name: My First Action
# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ dev, test, main ]
  pull_request:
    branches: [ dev, test, main ]
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
* [GitHub ActionsWorkflowに指定しているubuntu-latestが18.04から20.04に！あなたのWorkflowは大丈夫？年末年始休暇前に要チェック | Developers.IO](https://dev.classmethod.jp/articles/check-runs-on-params-on-workflow/)
	* [Ubuntu-latest workflows will use Ubuntu-20.04 #1816](https://github.com/actions/virtual-environments/issues/1816)