---
title: "Introduction"
layout: "../../layouts/Blog.astro"
---

Hi ～ 我是 fsyud 🧸

| 姓名       | dy                                                                                                                                             |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| 装备       | MacBook Pro、iphone11、apple watch S6、Kindle                                                                                                  |
| 幸运数字   | 7                                                                                                                                              |
| 喜欢的电影 | 《洛奇》《海上钢琴师》《猫鼠游戏》《钢琴家》《黑客帝国》《飞越疯人院》《功夫》《功夫足球》《达拉斯买家俱乐部》《三块广告牌》《波西米亚狂想曲》 |

全职前端开发

```js
const introduceMyself = () => {
  const name = "Karl";
  const profession = "Frontend Developer";
  const skills = [
    "HTML",
    "CSS",
    "JavaScript",
    "React",
    "Vue",
    "TypeScript",
    "nodejs",
    "nextjs",
    "kotlin",
    "mongodb",
    "ReactNative",
  ];
  const experienceYears = 5;

  console.log(
    `Hello! My name is ${name}. I am a ${profession} with ${experienceYears} years of experience.`
  );
  console.log("Here are some of the technologies I work with:");
  skills.forEach((skill) => {
    console.log(`- ${skill}`);
  });
};

introduceMyself();
```
