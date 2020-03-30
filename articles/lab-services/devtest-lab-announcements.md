---
title: Zaúčtování oznámení do testovacího prostředí v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Přečtěte si, jak přidat oznámení do testovacího prostředí v Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 84120b07de3a03a049493eb973b6dc46f8668387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976452"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Odeslání oznámení do testovacího prostředí v laboratořích Azure DevTest Labs

Jako správce testovacího prostředí můžete v existujícím testovacím prostředí zveřejnit vlastní oznámení, které uživatele upozorní na nedávné změny nebo dodatky k testovacímu prostředí. Můžete například uživatele informovat o:

- Nové velikosti virtuálních počítače, které jsou k dispozici
- Obrázky, které jsou aktuálně nepoužitelné
- Aktualizace zásad testovacího prostředí

Po odeslání se oznámení zobrazí na stránce Přehled testovacího prostředí a uživatel jej může vybrat pro další podrobnosti.

Funkce oznámení je určena pro dočasná oznámení.  Oznámení můžete snadno zakázat poté, co již není potřeba.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Postup odeslání oznámení v existující laboratoři

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby**a ze seznamu vyberte **DevTest Labs.** (Vaše testovací prostředí se už může zobrazit na řídicím panelu v části **Všechny prostředky**).
1. Ze seznamu testovacích prostředí vyberte testovací prostředí, ve kterém chcete zveřejnit oznámení.
1. V oblasti **Přehled** testovacího prostředí vyberte **Konfigurace a zásady**.

    ![Tlačítko Konfigurace a zásady](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Vlevo v části **NASTAVENÍ**vyberte **možnost Oznámení lab**.

    ![Tlačítko Oznámení v laboratoři](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Chcete-li vytvořit zprávu pro uživatele v tomto testovacím prostředí, nastavte **možnost Povoleno** na **ano**.

1. Můžete zadat **datum vypršení platnosti** a zadat datum a čas, po kterém se oznámení již uživatelům nezobrazuje. Pokud nezadáte datum vypršení platnosti, oznámení zůstane, dokud ho nezakážete.

   > [!NOTE]
   > Po vypršení platnosti oznámení se již uživatelům nezobrazuje, ale stále existuje v podokně **oznámení testovacího prostředí.** Můžete provést úpravy a znovu ji povolit, aby byla znovu aktivní.
   >
   >

1. Zadejte **název oznámení** a text **oznámení**.

   Název může mít až 100 znaků a zobrazí se uživateli na stránce Přehled testovacího prostředí. Pokud uživatel vybere název, zobrazí se text oznámení.

   Text oznámení přijímá markdown. Při zadávání textu oznámení můžete zprávu zobrazit v oblasti Náhled v dolní části obrazovky.

    ![Lab oznámení obrazovky k vytvoření zprávy.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Až bude oznámení připravené k odeslání, vyberte **Uložit.**

Pokud již nechcete zobrazovat toto oznámení uživatelům testovacího prostředí, vraťte se na stránku **oznámení testovacího prostředí** a nastavte **možnost Povoleno** na **ne**. Pokud jste zadali datum vypršení platnosti, oznámení bude v daný den a čas automaticky zakázáno.

## <a name="steps-for-users-to-view-an-announcement"></a>Postup zobrazení oznámení pro uživatele

1. Na [webu Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)vyberte testovací prostředí.

1. Pokud pro něj laboratoř zveřejnila oznámení, zobrazí se v horní části stránky Přehled testovacího prostředí informační oznámení. Toto oznámení informací je název oznámení, který byl zadán při vytvoření oznámení.

    ![Oznámení laboratoře na stránce Přehled](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Uživatel může vybrat zprávu pro zobrazení celého oznámení.

    ![Více informací k oznámení laboratoře](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
Oznámení můžete zadat jako součást šablony Azure Resource Manager, jak je znázorněno v následujícím příkladu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

Šablonu Azure Resource Manageru můžete nasadit jedním z následujících způsobů:

- [Portál Azure](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [ROZHRANÍ API PRO ODPOČINEK](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>Další kroky
* Pokud změníte nebo nastavíte zásady testovacího prostředí, můžete zveřejnit oznámení, které informuje uživatele. [Nastavení zásad a plánů](devtest-lab-set-lab-policy.md) poskytuje informace o použití omezení a konvencí v rámci předplatného pomocí přizpůsobených zásad.
* Prozkoumejte [galerii šablon rychlého startu nástroje DevTest Labs Azure .](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
