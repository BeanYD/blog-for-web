outlook注意事项

## 注意点

1.尽量使用table+tr+th+td的组合，使用strong标签加粗，span标签分离，不使用div

2.table的padding会被th的同意padding方向覆盖

3.table中的style样式，padding不生效

4.style样式中格式语法严格，不能有多余的符号如：`;`

5.img标签的样式，使用属性添加，如`width`，在outlook中，`style:"width:50px"`不生效，使用`width="50"`才能生效

整个<tb>标签生效，不仅仅是<a>标签可以跳转链接，参考greenmangaming的激活邮件中的按钮实现

```html
<table>
    <tbody>
        ...
        <tr>
            <td align="center" style="background-color: #ffffff;">
                <a href="https://..." style="text-decoration: none;" target="_blank">
                    <table bgcolor="#5373DB" height="52" width="100%" border="0" cellpadding="0" cellspacing="0">
                        <tbody>
                            <tr>
                                <td style="font-size: 16px; letter-spacing: 0.8px; text-transform: uppercase; text-align: center; width: 100%; border-radius: 4px;">
                                    <span>
                                        <a href="https://..." style="text-decoration: none; color: #ffffff;" target="_blank">
                                            激活帐户
                                        </a>
                                    </span>
                                </td>
                                <td style="padding-right: 22px;">
                                    <img src="https://..." width="22" />
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </a>
            </td>
        </tr>
        ...
    </tbody>
</table>
```

