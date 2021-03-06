---
title: 教程：创建 LUIS 应用以获取位置数据 - Azure | Microsoft Docs
description: 本教程介绍如何使用意向和分层实体创建一个简单的 LUIS 应用，以提取数据。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 41f74ff00e4fad751d4a2b7ae96ebb048bbcdfcd
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492692"
---
# <a name="tutorial-5-add-hierarchical-entity"></a>教程：5. 添加分层的实体
在本教程中，我们将创建一个应用，用于演示如何根据上下文查找相关的数据片段。 

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解分层实体和根据上下文学习到的子级 
> * 在人力资源 (HR) 域中使用 LUIS 应用 
> * 添加包含出发地和目的地子级的位置分层实体
> * 训练并发布应用
> * 查询应用的终结点以查看包含分层子级的 LUIS JSON 响应 

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>开始之前
如果还没有[列表实体](luis-quickstart-intent-and-list-entity.md)教程中所述的人力资源应用，请将 JSON [导入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 网站上的一个新应用中。 要导入的应用位于 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-list-HumanResources.json) Github 存储库中。

若要保留原始人力资源应用，请在[设置](luis-how-to-manage-versions.md#clone-a-version)页上克隆版本，并将其命名为 `hier`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 

## <a name="purpose-of-the-app-with-this-entity"></a>包含此实体的应用的用途
此应用用于确定在何处将员工从原始位置（大楼和办公室）移到目标位置（大楼和办公室）。 它使用分层实体来确定陈述中的位置。 

分层实体非常适合此类数据，因为这两个数据片段：

* 在使用陈述的情况下彼此相关。
* 使用特别选择的词汇来指示每个位置。 这些词汇的示例包括：from/to（从/到）、leaving/headed to（离开/前往）、away from/toward（离开/前往）。
* 两个位置往往在同一个陈述中。 

**分层**实体的用途是根据上下文查找陈述中的相关数据。 考虑以下陈述：

```JSON
mv Jill Jones from a-2349 to b-1298
```
该陈述中指定了两个位置：`a-2349` 和 `b-1298`。 假定字母对应于大楼名称，数字表示该大楼中的办公室。 分组时，可以将它们作为分层实体 `Locations` 的子级，因为这两种数据都需要从陈述中提取，而且彼此相关。 
 
如果只存在分层实体的一个子级（出发地或目的地），仍会提取该子级。 不需要找到所有子级即可提取一个或一些子级。 

## <a name="remove-prebuilt-number-entity-from-app"></a>从应用中删除预生成的数字实体
若要查看整个陈述并标记分层子级，请暂时删除预生成的数字实体。

1. LUIS 的“生成”部分包含你的人力资源应用。 在右上方的菜单栏中选择“生成”可切换到此部分。 

2. 在左侧菜单中选择“实体”。

3. 选择列表中数字实体右侧的省略号 (***...***) 按钮。 选择“删除”。 

    [ ![实体列表页上 LUIS 应用的屏幕截图，已针对“数字”预生成实体突出显示删除按钮](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png)](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png#lightbox)


## <a name="add-utterances-to-moveemployee-intent"></a>将表述添加到 MoveEmployee 意向

1. 在左侧菜单中选择“意向”。

2. 从意向列表中选择“MoveEmployee”。

    [ ![LUIS 应用的屏幕截图，已在左菜单中突出显示 MoveEmployee 意向](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png)](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png#lightbox)

3. 添加以下示例陈述：

    |示例陈述|
    |--|
    |将 John W. Smith 移**到** a-2345|
    |要求 Jill Jones 转**到** b-3499|
    |组织 x23456 **从** hh-2345 移**到** e-0234|
    |开始文书工作，将 x12345 设置为**离开** a-3459，**前往** f-34567|
    |让 425-555-0000 **离开** g-2323，**前往** hh-2345|

    [ ![LUIS 的屏幕截图，在 MoveEmployee 意向中有新陈述](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    在[列表实体](luis-quickstart-intent-and-list-entity.md)教程中，可以通过姓名、电子邮件地址、电话分机、移动电话号码或美国联邦社会安全号码来指定某个员工。 这些员工编号用在陈述中。 前面的示例陈述包括了用于表示原始位置和目标位置（以粗体标记）的不同方式。 一些陈述特意只包含目标位置。 这有助于 LUIS 了解在不指定原始位置的情况下，如何在陈述中放置这些位置。     

## <a name="create-a-location-entity"></a>创建位置实体
LUIS 需要通过在陈述中标记原始位置和目标位置来了解什么是位置。 如需在令牌（原始）视图中查看陈述，请在标记为“实体视图”的陈述上方的栏中选择切换。 切换开关以后，此控件会被标记为“令牌视图”。

1. 在陈述 `Displace 425-555-0000 away from g-2323 toward hh-2345` 中，选择单词 `g-2323`。 此时会出现下拉菜单，其顶部有一个文本框。 在文本框中输入实体名称 `Locations`，然后在下拉菜单中选择“创建新实体”。 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "在意向页上创建新实体的屏幕截图")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. 在弹出窗口中，选择包含 `Origin` 和 `Destination` 子实体的“分层”实体类型。 选择“完成”。

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "用于创建新位置实体的弹出对话框的屏幕截图")

3. `g-2323` 的标签已标记为 `Locations`，因为 LUIS 不知道名词是出发地还是目的地，或者两者都不是。 依次选择 `g-2323`、“位置”，遵循菜单操作，然后选择 `Origin`。

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "实体的屏幕截图，该实体标记用于更改位置实体子级的弹出对话框")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. 标记所有其他陈述中的其他位置，方法是：选择陈述中的大楼和办公室，然后选择“位置”，再遵循右侧的菜单选择 `Origin` 或 `Destination`。 所有位置都标记以后，“令牌视图”中的陈述开始显示出某种模式。 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "在陈述中标记的“位置”实体的屏幕截图")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>向应用添加预生成的数字实体
将预生成的数字实体添加回应用程序。

1. 在左侧导航菜单中选择“实体”。

2. 选择“管理预生成的实体”按钮。

    [ ![“实体”列表的屏幕截图，已突出显示“管理预生成的实体”](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png#lightbox)

3. 从预生成的实体的列表中选择“数字”，然后选择“完成”。

    ![在“预生成的实体”对话框中选择的数字的屏幕截图](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>训练 LUIS 应用

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的话语查询终结点

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. 将光标定位到地址栏中 URL 的末尾，并输入 `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 此话语不同于标记的任何话语，因此，它非常适合用于测试，测试结果应返回包含所提取的分层实体的 `MoveEmployee` 意向。

  ```JSON
  {
    "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9966052
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9966052
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0325253047
      },
      {
        "intent": "FindForm",
        "score": 0.006137873
      },
      {
        "intent": "GetJobInformation",
        "score": 0.00462633232
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00415637763
      },
      {
        "intent": "ApplyForJob",
        "score": 0.00382325822
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00249120337
      },
      {
        "intent": "None",
        "score": 0.00130756292
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00119622645
      },
      {
        "intent": "Utilities.Confirm",
        "score": 1.26910036E-05
      }
    ],
    "entities": [
      {
        "entity": "jill - jones @ mycompany . com",
        "type": "Employee",
        "startIndex": 18,
        "endIndex": 41,
        "resolution": {
          "values": [
            "Employee-45612"
          ]
        }
      },
      {
        "entity": "x - 2345",
        "type": "Locations::Origin",
        "startIndex": 48,
        "endIndex": 53,
        "score": 0.8520272
      },
      {
        "entity": "g - 23456",
        "type": "Locations::Destination",
        "startIndex": 58,
        "endIndex": 64,
        "score": 0.974032
      },
      {
        "entity": "-2345",
        "type": "builtin.number",
        "startIndex": 49,
        "endIndex": 53,
        "resolution": {
          "value": "-2345"
        }
      },
      {
        "entity": "-23456",
        "type": "builtin.number",
        "startIndex": 59,
        "endIndex": 64,
        "resolution": {
          "value": "-23456"
        }
      }
    ]
  }
  ```

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>是否可以为每个位置使用正则表达式？
是的，可以创建包含原始位置和目标位置角色的正则表达式，然后将其用在某个模式中。

此示例中的位置（例如 `a-1234`）遵循特定的格式，即以一到两个字母开头，后接破折号，然后是一系列数字（4 到 5 个）。 可以将该数据描述为一个正则表达式实体，每个位置有一个角色。 角色适用于模式。 可以根据这些陈述创建模式，然后针对位置格式创建一个正则表达式，再将其添加到模式。 <!-- Go to this tutorial to see how that is done -->

## <a name="patterns-with-roles"></a>将模式与角色配合使用

[!include[LUIS Compare hierarchical entities to patterns with roles](../../../includes/cognitive-services-luis-hier-roles.md)]

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用只包含数个意向和一个分层实体，识别了自然语言查询意向，并返回了提取的数据。 

现在，聊天机器人已获得足够的信息，可以确定确定主要操作 `MoveEmployee`，以及在陈述中找到的位置信息。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 调用方应用程序（例如聊天机器人）可以提取 topScoringIntent 结果和实体中的数据，以执行下一步骤。 LUIS 不会针对机器人或调用方应用程序执行编程工作。 LUIS 只确定用户的意向是什么。 

## <a name="clean-up-resources"></a>清理资源

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [了解如何添加复合实体](luis-tutorial-composite-entity.md) 