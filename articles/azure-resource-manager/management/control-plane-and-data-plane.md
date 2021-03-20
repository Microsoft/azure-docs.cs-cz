---
title: Operace řídicí roviny a roviny dat
description: Popisuje rozdíl mezi operacemi řídicí roviny a roviny dat. Operace roviny ovládacího prvku jsou zpracovávány Azure Resource Manager. Operace roviny dat je zpracovávána službou.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: f478037c3e83c1d28cc900d64512a41619628dd7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91371265"
---
# <a name="azure-control-plane-and-data-plane"></a>Rovina ovládacího prvku Azure a rovina dat

Operace Azure je možné rozdělit do dvou kategorií – rovina ovládacího prvku a rovina dat. Tento článek popisuje rozdíly mezi těmito dvěma typy operací.

K správě prostředků ve vašem předplatném používáte rovinu ovládacího prvku. Rovina dat slouží k používání schopností vystavených vaší instancí typu prostředku.

Například:

* Virtuální počítač vytvoříte přes řídicí plochu. Po vytvoření virtuálního počítače s ním budete pracovat prostřednictvím operací roviny dat, jako je například protokol RDP (Remote Desktop Protocol) (RDP).

* Účet úložiště vytvoříte pomocí roviny ovládacího prvku. Rovina dat se používá ke čtení a zápisu dat v účtu úložiště.

* Databázi Cosmos vytvoříte přes řídicí plochu. K dotazování dat v databázi Cosmos použijte rovinu dat.

## <a name="control-plane"></a>Řídicí rovina

Všechny požadavky na operace roviny ovládacího prvku se odesílají na adresu Azure Resource Manager URL. Tato adresa URL se liší podle prostředí Azure.

* V případě globálního úložiště Azure je adresa URL `https://management.azure.com` .
* V případě Azure Government adresa URL je `https://management.usgovcloudapi.net/` .
* V případě Azure Německo je adresa URL `https://management.microsoftazure.de/` .
* V případě Microsoft Azure Čína 21Vianet je adresa URL `https://management.chinacloudapi.cn` .

Pokud chcete zjistit, které operace používají adresu URL Azure Resource Manager, přečtěte si téma [Azure REST API](/rest/api/azure/). Například [operace vytvořit nebo aktualizovat](/rest/api/mysql/databases/createorupdate) pro MySQL je operace řízení roviny, protože adresa URL požadavku je:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure Resource Manager zpracovává všechny požadavky na řídicí rovinu. Automaticky použije funkce Azure, které jste implementovali pro správu prostředků, například:

* [Řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [Zámky pro správu](lock-resources.md)
* [Protokoly aktivit](view-activity-logs.md)

Po ověření žádosti Azure Resource Manager odesílá poskytovateli prostředků, který operaci dokončí.

Rovina ovládacího prvku zahrnuje dva scénáře zpracování požadavků – "zelená pole" a "hnědá pole". Zelené pole odkazuje na nové prostředky. Hnědá pole odkazuje na existující prostředky. Při nasazení prostředků Azure Resource Manager pochopit, kdy vytvořit nové prostředky a kdy aktualizovat existující prostředky. Nemusíte se obávat, že se vytvoří identické prostředky.

## <a name="data-plane"></a>Rovina dat

Požadavky na operace roviny dat se odesílají do koncového bodu, který je specifický pro vaši instanci. Například [operace rozpoznávání jazyka](/rest/api/cognitiveservices/textanalytics/detect%20language/detect%20language) v Cognitive Services je operace roviny dat, protože adresa URL požadavku je:

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

Operace roviny dat nejsou omezeny na REST API. Můžou vyžadovat další přihlašovací údaje, třeba přihlášení k virtuálnímu počítači nebo databázovému serveru.

Funkce, které vynutily správu a řízení, se nemusí uplatňovat na operace roviny dat. Je potřeba vzít v úvahu různé způsoby interakce uživatelů s vašimi řešeními. Například zámek, který brání uživatelům v odstraňování databáze, nebrání uživatelům odstraňovat data prostřednictvím dotazů.

K řízení operací roviny dat můžete použít některé zásady. Další informace najdete v tématu [režimy poskytovatele prostředků (Preview) v Azure Policy](../../governance/policy/concepts/definition-structure.md#resource-provider-modes).

## <a name="next-steps"></a>Další kroky

* Přehled Azure Resource Manager najdete v tématu [co je Azure Resource Manager?](overview.md)

* Další informace o vlivu definic zásad na nové prostředky a stávající prostředky najdete v tématu [vyhodnocení dopadu nové definice Azure Policy](../../governance/policy/concepts/evaluate-impact.md).
