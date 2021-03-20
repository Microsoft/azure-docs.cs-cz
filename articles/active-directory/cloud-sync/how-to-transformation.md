---
title: Azure AD Connect transformace synchronizace cloudu
description: Tento článek popisuje, jak pomocí transformací změnit výchozí mapování atributů.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 1c81ee0ebace65e50cdab5b5b223d5e5eae4ff9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613259"
---
# <a name="transformations"></a>Transformace

Pomocí transformace můžete změnit výchozí chování při synchronizaci atributu s Azure Active Directory (Azure AD) pomocí cloudové synchronizace.

Chcete-li provést tuto úlohu, je nutné upravit schéma a poté jej znovu odeslat prostřednictvím webové žádosti.

Další informace o atributech synchronizace cloudu najdete v tématu [Principy schématu Azure AD](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Načtení schématu
Chcete-li načíst schéma, postupujte podle kroků v [části zobrazení schématu](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Mapování vlastního atributu
Chcete-li přidat mapování vlastního atributu, postupujte podle těchto kroků.

1. Zkopírujte schéma do editoru textu nebo kódu, jako je například [Visual Studio Code](https://code.visualstudio.com/).
1. Vyhledejte objekt, který chcete ve schématu aktualizovat.

   ![Objekt ve schématu](media/how-to-transformation/transform-1.png)</br>
1. Vyhledejte kód `ExtensionAttribute3` v rámci objektu User.

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
1. Upravte kód tak, aby byl atribut společnosti namapován na `ExtensionAttribute3` .

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
 1. Zkopírujte schéma zpátky do Graph Exploreru, změňte **typ žádosti** na **Put** a vyberte **Spustit dotaz**.

    ![Spustit dotaz](media/how-to-transformation/transform-2.png)

 1. Nyní v Azure Portal přejdete do konfigurace synchronizace cloudu a vyberte **restartovat zřizování**.

    ![Restartovat zřizování](media/how-to-transformation/transform-3.png)

 1. Po chvíli ověřte, zda jsou atributy naplněny spuštěním následujícího dotazu v aplikaci Graph Explorer: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}` .
 1. Nyní by se měla zobrazit hodnota.

    ![Hodnota se zobrazí](media/how-to-transformation/transform-4.png)

## <a name="custom-attribute-mapping-with-function"></a>Mapování vlastního atributu pomocí funkce
Pro pokročilejší mapování můžete použít funkce, které vám umožní manipulovat s daty a vytvářet hodnoty pro atributy, které vyhovují potřebám vaší organizace.

Chcete-li provést tuto úlohu, postupujte podle předchozích kroků a pak upravte funkci, která se používá k vytvoření konečné hodnoty.

Informace o syntaxi a příkladech výrazů naleznete v tématu [zápis výrazů pro mapování atributů v Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)
