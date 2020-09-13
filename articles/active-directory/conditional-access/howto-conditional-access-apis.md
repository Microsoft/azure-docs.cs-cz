---
title: Rozhraní API pro podmíněný přístup a PowerShell – Azure Active Directory
description: Použití rozhraní API podmíněného přístupu Azure AD a prostředí PowerShell ke správě zásad, jako je kód
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33f3766e02316eccb519bea15246541531750ed1
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007995"
---
# <a name="conditional-access-programmatic-access"></a>Podmíněný přístup: programový přístup

Řada organizací si vyjádřila potřebu spravovat co nejvíc různých prostředí, jako je například kód. Pomocí Microsoft Graph můžete zacházet se zásadami podmíněného přístupu, jako jsou jakékoli jiné části kódu ve vašem prostředí.

Microsoft Graph poskytuje jednotný model programovatelnosti, který mohou organizace použít k interakci s daty v Microsoft 365, Windows 10 a Enterprise Mobility + Security. Další informace o Microsoft Graph najdete v článku [přehled Microsoft Graph](/graph/overview).

![Obrázek znázorňující primární prostředky a vztahy, které jsou součástí grafu](./media/howto-conditional-access-apis/microsoft-graph.png)

K dispozici jsou následující příklady bez podpory. Tyto příklady můžete použít jako základ pro nástroje ve vaší organizaci. 

Mnohé z následujících příkladů používají nástroje, jako jsou [spravované identity](../managed-identities-azure-resources/overview.md), [Logic Apps](../../logic-apps/logic-apps-overview.md), [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [týmy](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/)a [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="configure"></a>Konfigurace

### <a name="powershell"></a>PowerShell

Pro mnoho správců už PowerShell je srozumitelný skriptovací nástroj. Následující příklad ukazuje, jak použít [modul Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD) ke správě zásad podmíněného přístupu.

- [Konfigurace zásad podmíněného přístupu pomocí příkazů Azure AD PowerShellu](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>Graph API

Tento příklad ukazuje možnosti základní operace vytvoření, čtení, aktualizace a odstranění (CRUD), které jsou k dispozici v rozhraní API pro podmíněný přístup grafu. Příklad obsahuje také některé šablony JSON, které můžete použít k vytvoření některých ukázkových zásad.

- [Konfigurace zásad podmíněného přístupu pomocí Microsoft Graph volání rozhraní API](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>Konfigurace pomocí šablon

Použijte rozhraní API pro podmíněný přístup k nasazení zásad podmíněného přístupu v předprodukčním prostředí pomocí šablony.

- [Konfigurace zásad podmíněného přístupu pomocí šablon Microsoft Graph API](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>Test

Tento příklad modeluje bezpečnější postupy nasazení s pracovními postupy schvalování, které mohou kopírovat zásady podmíněného přístupu z jednoho prostředí, jako je například předprodukční, do jiného, podobně jako v produkčním prostředí.

- [Zvýšení úrovně zásad podmíněného přístupu z testovacích prostředí](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>Nasazení

V tomto příkladu je k dispozici mechanismus pro postupné provádění zásad podmíněného přístupu k naplnění uživatelů, což vám umožní včas spravovat dopad na podporu a odhalit problémy.

- [Nasazení zásad podmíněného přístupu do produkčních prostředí s pracovními postupy schvalování](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>Monitorování

Tento příklad poskytuje mechanismus pro monitorování změn zásad podmíněného přístupu v průběhu času a může aktivovat upozornění při změně klíčových zásad.

- [Monitorování nasazených zásad podmíněného přístupu pro změny a aktivační výstrahy](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>Správa

### <a name="backup-and-restore"></a>Zálohování a obnovení

Pomocí tohoto příkladu Automatizujte zálohování a obnovení zásad podmíněného přístupu pomocí schválení v týmech.

- [Správa procesu zálohování a obnovení zásad podmíněného přístupu pomocí Microsoft Graph volání rozhraní API](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>Účty pro nouzový přístup

Několik správců může vytvářet zásady podmíněného přístupu a může pro ně přidat [účty pro nouzový přístup](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) jako vyloučení těchto zásad. V tomto příkladu se zajistí, že se všechny zásady aktualizují tak, aby zahrnovaly určené účty pro nouzový přístup.

- [Správa přiřazování účtů pro nouzový přístup k zásadám podmíněného přístupu pomocí volání rozhraní API Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>Plánování řešení nepředvídaných událostí

Věci nejsou vždy funkční, pokud k tomu dojde, budete potřebovat způsob, jak se vrátit do stavu, ve kterém může práce pokračovat. Následující příklad poskytuje způsob, jak obnovit zásady na známý dobrý pohotovostní plán a zakázat další zásady podmíněného přístupu.

- [Správa aktivace zásad pro nepředvídané podmíněné přístupy pomocí Microsoft Graph volání rozhraní API](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>Příspěvek komunity

Tyto ukázky jsou k dispozici v našem [úložišti GitHub](https://github.com/Azure-Samples/azure-ad-conditional-access-apis). Jsme rádi, že budeme podporovat komunitní příspěvky na GitHubu a žádosti o přijetí změn.

## <a name="next-steps"></a>Další kroky

- [Přehled Microsoft Graphu](/graph/overview)

- [Rozhraní API pro podmíněný přístup](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-1.0)

- [Rozhraní API pojmenovaného umístění](/graph/api/resources/namedlocation?view=graph-rest-1.0)
