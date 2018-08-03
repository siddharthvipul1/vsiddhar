```
- job:
    name: distributed-regression
    node: regression7
    description: Run regression with distributed test framework
    project-type: freestyle
    concurrent: true

    scm:
      - git:
          branches:
          - $GERRIT_BRANCH
          refspec: $GERRIT_REFSPEC
          choosing-strategy: gerrit
          url: git://review.gluster.org/glusterfs.git

    properties:
      - discard-after-x:
          x: 30
      - one-build-per-node

    parameters:
      - string:
          default: refs/heads/master
          description: 'For review 12345 and patch set 6, this will be
refs/changes/45/12345/6. Default: Tip of master'
          name: GERRIT_REFSPEC
      - string:
          default: master
          description: 'Name of the branch you want to build from. We
usually build from master'
          name: GERRIT_BRANCH
      - string:
          default: '8'
          description: Number of machines to launch
          name: MACHINES_COUNT

    builders:
      - shell: !include-raw: ../scripts/distributed-regression.sh

    triggers:
      - timed: '0 */12 * * *'

    publishers:
      - archive:
          allow-empty: true
          artifacts: "failed-tests-logs.tgz"
          only-if-success: true

    wrappers:
    - timestamps
    - credentials-binding:
           - username-password-separated:
               credential-id: e423d7f9-edaf-45ea-aa8f-9c3f8e6563f1
               username: USERNAME
               password: PASSWORD
           - file:
               credential-id: http_int_credential
               variable: LOG_KEY
```
