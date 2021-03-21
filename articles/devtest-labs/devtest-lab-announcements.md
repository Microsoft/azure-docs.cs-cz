---
title: Zveřejnění oznámení do testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: Naučte se publikovat vlastní oznámení v existujícím testovacím prostředí a upozornit uživatele na nedávné změny nebo dodatky do testovacího prostředí v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 72c6ed7ab691ece094673ff5a158ffa769a9f7a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88270763"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Zveřejnění oznámení do testovacího prostředí v Azure DevTest Labs

Jako správce testovacího prostředí můžete publikovat vlastní oznámení v existujícím testovacím prostředí a upozornit uživatele na nedávné změny nebo dodatky do testovacího prostředí. Například může být vhodné informovat uživatele o:

- Nové velikosti virtuálních počítačů, které jsou k dispozici
- Obrázky, které jsou aktuálně nepoužitelné
- Aktualizace zásad testovacího prostředí

Po odeslání se oznámení zobrazí na stránce Přehled testovacího prostředí a uživatel ho může vybrat.

Funkce oznámení je určena k použití pro dočasná oznámení.  Oznámení můžete snadno zakázat poté, co už nebude potřeba.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Postup zveřejnění oznámení v existujícím testovacím prostředí

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. V případě potřeby vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** . (Vaše testovací prostředí se už možná zobrazuje na řídicím panelu v části **všechny prostředky**).
1. V seznamu cvičení vyberte testovací prostředí, ve kterém chcete publikovat oznámení.
1. V oblasti **Přehled** testovacího prostředí vyberte **Konfigurace a zásady**.

    ![Tlačítko Konfigurace a zásady](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Vlevo v části **Nastavení** vyberte **oznámení testovacího prostředí**.

    ![Tlačítko oznámení testovacího prostředí](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Pokud chcete vytvořit zprávu pro uživatele v tomto testovacím prostředí, nastavte možnost **povoleno** na **Ano**.

1. Můžete zadat **Datum vypršení platnosti** a zadat datum a čas, po kterém se oznámení již nebude zobrazovat uživatelům. Pokud nezadáte datum vypršení platnosti, oznámení zůstane, dokud ho nezakážete.

   > [!NOTE]
   > Po vypršení platnosti oznámení se už nebude zobrazovat uživatelům, ale pořád existuje v podokně **oznámení testovacího prostředí** . Můžete je upravit a znovu aktivovat, aby se znovu aktivoval.
   >
   >

1. Zadejte **název oznámení** a **Text oznámení**.

   Název může mít až 100 znaků a zobrazí se uživateli na stránce s přehledem testovacího prostředí. Pokud uživatel vybere název, zobrazí se text oznámení.

   Text oznámení přijímá Markdownu. Když zadáte text oznámení, můžete zobrazit zprávu v oblasti náhledu v dolní části obrazovky.

    ![Obrazovka oznámení testovacího prostředí pro vytvoření zprávy.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Pokud je oznámení připravené k publikování, vyberte **Uložit** .

Když už nechcete zobrazovat toto oznámení pro uživatele testovacího prostředí, vraťte se na stránku **oznámení testovacího prostředí** a nastavte **Povolit** na **ne**. Pokud jste zadali datum vypršení platnosti, oznámení se v tomto datu a čase automaticky zakáže.

## <a name="steps-for-users-to-view-an-announcement"></a>Postup pro uživatele, kteří si můžou zobrazit oznámení

1. Z [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)vyberte testovací prostředí.

1. Pokud je pro testovací prostředí vyslané oznámení, zobrazí se v horní části stránky s přehledem testovacího prostředí informační oznámení. Tato informace je název oznámení, který jste zadali při vytváření oznámení.

    ![Oznámení testovacího prostředí na stránce s přehledem](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Uživatel může vybrat zprávu pro zobrazení celého oznámení.

    ![Další informace o oznámení testovacího prostředí](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

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

Šablonu Azure Resource Manager můžete nasadit pomocí některého z následujících způsobů:

- [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [REST API](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>Další kroky
* Pokud změníte nebo nastavíte zásady testovacího prostředí, budete možná chtít zveřejnit oznámení, abyste uživatelům informovali. [Nastavení zásad a plánů](devtest-lab-set-lab-policy.md) poskytuje informace o použití omezení a konvencí v rámci předplatného pomocí přizpůsobených zásad.
* Prozkoumejte [galerii šablon rychlého startu Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
