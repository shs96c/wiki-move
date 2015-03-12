#How to build with Buck - in ALPHA (not even beta!!)

# Building Selenium with Buck

```
git clone https://github.com/shs96c/buck.git
cd buck && ant
export PATH=`pwd`/bin:$PATH
cd ~/src/selenium 
buck build chrome firefox htmlunit remote leg-rc
buck test --all
```