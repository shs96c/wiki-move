Set the env vars:

SAUCE\_USERNAME=selenium-ci

SAUCE\_APIKEY=OUR\_SECRET\_KEY\_WHICH\_YOU\_CAN\_GET\_FROM\_DAWAGNER

Run ./go debug-server somewhere publicly accessible - ci.seleniumhq.org:2[34](34.md)10 host these files at HEAD

USE\_SAUCE=true HOSTNAME=public.host.name TEST\_HTTP\_PORT=2310 TEST\_HTTPS\_PORT=2410 SAUCE\_SELENIUM\_VERSION=http://url/to/version/of/selenium-server-standalone.jar SAUCE\_BROWSER\_VERSION=10 SAUCE\_OS=xp SAUCE\_JOB\_NAME=my-job-name ./go test\_firefox method=myTest

SAUCE\_OS is a stringification of something recognised by the Platform Java enum.