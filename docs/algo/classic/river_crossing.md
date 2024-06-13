---
date: 2024-04-28
categories:
  - Algo
search:
  boost: 2
hide:
  - footer
---

## 问题

人、狼、羊、白菜渡河问题：(狐狸、鹅、豆子问题) 人、狼、羊、白菜要从河的此岸借由一艘船渡河至另一岸，其中只有人会划船，每次人只能带一件东西搭船渡河， 且狼和羊、羊和白菜不能在无人监控的情况下放在一起。 在这些条件下，在最小渡河次数下如何才能让大家都渡河至另一河岸?

> Refer: https://zh.wikipedia.org/zh-cn/%E6%B8%A1%E6%B2%B3%E5%95%8F%E9%A1%8C

## 求解

### 解法1

| No  | 左岸                       | 右岸                       | 说明         |
| --- | -------------------------- | -------------------------- | ------------ |
| 0   | [farmer,wolf,goat,cabbage] | []                         |              |
| 1   | [wolf,cabbage]             | [farmer,goat]              | 农夫带羊过河 |
| 2   | [farmer,wolf,cabbage]      | [goat]                     | 农夫返回     |
| 3   | [cabbage]                  | [farmer,wolf,goat]         | 农夫带狼过河 |
| 4   | [farmer,goat,cabbage]      | [wolf]                     | 农夫带羊返回 |
| 5   | [goat]                     | [farmer,wolf,cabbage]      | 农夫带菜过河 |
| 6   | [farmer,goat]              | [wolf,cabbage]             | 农夫返回     |
| 7   | []                         | [farmer,wolf,goat,cabbage] | 农夫带羊过河 |

### 解法2

| No  | 左岸                       | 右岸                       | 说明         |
| --- | -------------------------- | -------------------------- | ------------ |
| 0   | [farmer,wolf,goat,cabbage] | []                         |              |
| 1   | [wolf,cabbage]             | [farmer,goat]              | 农夫带羊过河 |
| 2   | [farmer,wolf,cabbage]      | [goat]                     | 农夫返回     |
| 3   | [wolf]                     | [farmer,goat,cabbage]      | 农夫带菜过河 |
| 4   | [farmer,wolf,goat]         | [cabbage]                  | 农夫带羊返回 |
| 5   | [goat]                     | [farmer,wolf,cabbage]      | 农夫带狼过河 |
| 6   | [farmer,goat]              | [wolf,cabbage]             | 农夫返回     |
| 7   | []                         | [farmer,wolf,goat,cabbage] | 农夫带羊过河 |

## 代码


```py title="Python"
# -*- coding: UTF-8 -*-
import pandas as pd

name = ["farmer", "wolf", "goat", "cabbage"]
scheme_count = 0


# 完成
def is_done(status):
    return status[0] and status[1] and status[2] and status[3]


# 生成下一个局面的所有情况
def create_all_next_status(status):
    next_status_list = []

    for i in range(0, 4):
        if status[0] != status[i]:  # 和农夫不同一侧？
            continue

        next_status = [status[0], status[1], status[2], status[3]]
        # 农夫和其中一个过河，i 为 0 时候，农夫自己过河。
        next_status[0] = not next_status[0]
        next_status[i] = next_status[0]  # 和农夫一起过河

        if is_valid_status(next_status):
            next_status_list.append(next_status)

    return next_status_list


# 判断是否合法的局面
def is_valid_status(status):
    if status[1] == status[2]:
        if status[0] != status[1]:
            # 狼和羊同侧，没有人在场
            return False

    if status[2] == status[3]:
        if status[0] != status[2]:
            # 羊和草同侧，没有人在场
            return False

    return True


def search(history_status):
    global scheme_count
    current_status = history_status[len(history_status) - 1]

    next_status_list = create_all_next_status(current_status)
    for next_status in next_status_list:
        if next_status in history_status:
            # 出现重复的情况了
            continue

        history_status.append(next_status)

        if is_done(next_status):
            scheme_count += 1
            print("scheme " + str(scheme_count) + ":")
            print_history_status(history_status)
        else:
            search(history_status)

        history_status.pop()


def readable_status(status, is_across):
    result = ""
    for i in range(0, 4):
        if status[i] == is_across:
            if len(result) != 0:
                result += ","
            result += name[i]

    return "[" + result + "]"


# 打印结果
def print_history_status(history_status):
    left_items, right_items = [], []
    for status in history_status:
        left_items.append(readable_status(status, False))
        right_items.append(readable_status(status, True))
    data = {
        "左岸": left_items,
        "右岸": right_items,
    }
    df = pd.DataFrame(data)
    print(df)


if __name__ == "__main__":
    # 初始
    status = [False, False, False, False]
    # 保存历史状态
    history_status = [status]

    search(history_status)
    print("*" * 50)
    print("finish search, find " + str(scheme_count) + " scheme")

```
