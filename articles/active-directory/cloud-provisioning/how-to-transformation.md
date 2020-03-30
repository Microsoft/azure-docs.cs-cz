---
title: Transformace zřizování cloudu Azure AD Connect
description: Tento článek popisuje, jak pomocí transformací změnit výchozí mapování atributů.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549291"
---
# <a name="transformations"></a>Transformace

S transformací můžete změnit výchozí chování synchronizace atributu s Azure Active Directory (Azure AD) pomocí zřizování cloudu.

Chcete-li provést tento úkol, je třeba upravit schéma a potom jej znovu odeslat prostřednictvím webové žádosti.

Další informace o atributech zřizování cloudu najdete [v tématu Principy schématu Azure AD](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Načtení schématu
Chcete-li načíst schéma, postupujte podle kroků v [části Zobrazení schématu](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Mapování vlastních atributů
Chcete-li přidat vlastní mapování atributů, postupujte takto.

1. Zkopírujte schéma do textového editoru nebo editoru kódu, například [Visual Studio Code](https://code.visualstudio.com/).
1. Vyhledejte objekt, který chcete aktualizovat ve schématu.

   ![Objekt ve schématu](media/how-to-transformation/transform1.png)</br>
1. Vyhledejte kód `ExtensionAttribute3` pro pod objektem uživatele.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. Upravte kód tak, aby byl `ExtensionAttribute3`atribut společnosti mapován na .

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Zkopírujte schéma zpět do Průzkumníka grafů, změňte **typ požadavku** na **PUT**a vyberte **Spustit dotaz**.

    ![Spustit dotaz](media/how-to-transformation/transform2.png)

 1. Teď na webu Azure Portal přejděte na konfiguraci zřizování cloudu a vyberte **Restartovat zřizování**.

    ![Restartovat zřizování](media/how-to-transformation/transform3.png)

 1. Po chvíli ověřte, zda jsou atributy naplněny spuštěním `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`následujícího dotazu v aplikaci Graph Explorer: .
 1. Nyní byste měli vidět hodnotu.

    ![Zobrazí se hodnota](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Mapování vlastních atributů s funkcí
Pro pokročilejší mapování můžete použít funkce, které umožňují manipulovat s daty a vytvářet hodnoty pro atributy, které vyhovují potřebám vaší organizace.

Chcete-li provést tento úkol, postupujte podle předchozích kroků a upravte funkci, která se používá k vytvoření konečné hodnoty.

Informace o syntaxi a příkladech výrazů najdete v tématu [Zápis výrazů pro mapování atributů ve službě Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
