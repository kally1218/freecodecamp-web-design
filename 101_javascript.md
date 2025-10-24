# javascript的基本概念

1. underfined:定义了但未被赋值
2. 赋值语法:

   ```javascript
   let age = 25;
   # let的优势: 之后可以重新赋值
   age = 30;
   ```
3. cameCase:驼峰是命名法, 首词全部小写, 后面添加的单词每个首字母都要大写

   ```javascript
   let caseIsCameCase;
   ```
4. const 和 let的区别:

   ```javascript
   const maxScore = 100;
   # 不可更改变量的值,否则会报错, 并且在声明的时候就要赋值
   maxScore = 200; #报错
   ```

   1. console.log - F12的console(控制台)界面用于debug

      - 怎么用:

        - 方法一: html框架搭好后,
          ```html
          <!DOCTYPE html>
          <html lang="en"></html>
          <body>
              <script>
                  console.log("Hello, World!");

                  let num = 5;
                  console.log(num);
              </script>
          </body>
          </html>
          ```
        - `<script></script>` 里面直接写好方法, 然后打开网页,F12 选到console用于查看
      - 方法二:

        1. mac本地安装node

        ```bash
        brew install node
        ```

        2. 安装完成后验证

        ```bash
        node -v
        npm -v
        ```

        3. 终端执行
           ```bash
           node app.js
           ```
5. Semicolons - 分号";", 一行代码结束用的符号
6. comment 用什么语法

   1. single comment use "//"
   2. 多行 comment use"/**我是注释**/"
7.
