---
title: Azure AD Connect transformace zřizování cloudu
description: Tento dokument popisuje, jak pomocí transformací změnit výchozí mapování atributů.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 0d37fdb4ad0d385914aecd4ca62be498c5c0e7c5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794468"
---
# <a name="transformations"></a>Transformace

Transformace umožňuje změnit výchozí chování při synchronizaci atributu s Azure AD pomocí zřizování cloudu.  

Chcete-li provést tuto úlohu, je nutné upravit schéma a poté jej znovu odeslat prostřednictvím webové žádosti.

Další informace o atributech zřizování cloudu najdete v tématu [Principy schématu Azure AD](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Načtení schématu
Chcete-li načíst schéma, postupujte podle kroků uvedených v [části zobrazení schématu](concept-attributes.md#viewing-the-schema). 


## <a name="custom-attribute-mapping"></a>Mapování vlastního atributu
Chcete-li přidat mapování vlastního atributu, použijte následující postup.

1. Zkopírujte schéma do editoru textu nebo kódu, jako je například [Visual Studio Code](https://code.visualstudio.com/).  
2. Vyhledejte objekt, který chcete aktualizovat ve schématu ![](media/how-to-transformation/transform1.png)</br>
3. V objektu User vyhledejte kód pro **ExtensionAttribute3** .

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
 4.  Upravte kód tak, aby byl atribut společnosti mapován na ExtensionAttribute3.
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
 5. Zkopírujte schéma zpátky do Graph Exploreru, změňte typ požadavku na PUT a **Spusťte dotaz**.  
 ![](media/how-to-transformation/transform2.png)</br>
 6.  Nyní v Azure Portal přejděte do konfigurace zřizování cloudu a **restartujte zřizování**.
 ![](media/how-to-transformation/transform3.png)</br>
 7.  Po chvíli ověřte, že jsou atributy naplněny spuštěním následujícího dotazu v Graph Exploreru: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`.
 8.  Nyní by se měla zobrazit hodnota.
 ![](media/how-to-transformation/transform4.png)</br>

## <a name="custom-attribute-mapping-with-function"></a>Mapování vlastního atributu pomocí funkce
Pro pokročilejší mapování můžete použít funkce, které vám umožní manipulovat s daty a vytvářet hodnoty pro atribut tak, aby vyhovovaly potřebám vaší organizace.

Chcete-li provést tuto úlohu, stačí postupovat podle výše uvedených kroků a následně upravit funkci, která se používá k vytvoření konečné hodnoty.

Informace o syntaxi a příkladech výrazů naleznete v tématu [zápis výrazů pro mapování atributů v Azure Active Directory](reference-expressions.md)


## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřizování cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
