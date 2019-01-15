---
title: Integrace protokolů Azure s protokoly auditování Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat službu Azure Log Integration a integrace protokolů ze protokoly auditování Azure
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: c01d911a59647956914518aa62a616823241ab3b
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304920"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrace protokolů auditu Azure Active Directory

Události auditování Azure Active Directory (Azure AD) mohli snadno identifikovat privilegovaných akcí, ke kterým došlo v Azure Active Directory. Můžete zobrazit typy událostí, které můžete sledovat kontrolou [události sestavy auditování služby Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md).


>[!IMPORTANT]
> Funkce integrace protokolů Azure se přestanou používat podle 06/01/2019. Soubory ke stažení AzLog byly deaktivovány 27. června 2018. Pokyny, jak postupovat přesun vpřed revizi, příspěvek [použití Azure monitoru k integraci s nástroji SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Protokoly auditu kroků k integraci služby Azure Active Directory

> [!NOTE]
> Než se pokusíte kroky v tomto článku, musíte zkontrolovat [Začínáme](security-azure-log-integration-get-started.md) článku a proveďte příslušné kroky.

1. Otevřete příkazový řádek a spusťte tento příkaz:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Spusťte tento příkaz: 
 
   ``azlog createazureid``

   Tento příkaz vás vyzve k zadání přihlášení Azure. Příkaz vytvoří službu Azure Active Directory instančního objektu v tenantů Azure AD, které jsou hostiteli předplatná Azure, ve kterých přihlášený uživatel je správce, spolusprávce nebo vlastníka. Příkaz se nezdaří, pokud je uživatel přihlášený jenom uživatele typu Host do tenanta Azure AD. Ověřování do Azure se provádí prostřednictvím Azure AD. Při vytváření instančního objektu pro Azure Log Integration vytvoří identity Azure AD, který je přiřazen přístup ke čtení z předplatných Azure.

3. Spusťte následující příkaz, který poskytuje vaše ID tenanta. Musíte být členem role správce klienta ke spuštění příkazu.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Příklad:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Zkontrolujte následující složky potvrďte, že jsou v nich vytvořili soubory JSON protokolů auditu Azure Active Directory:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Toto video ukazuje kroky popsané v tomto článku:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Konkrétní pokyny k uvedení informace v souborech JSON do systému pro správu (SIEM) událostí a informací o zabezpečení obraťte se na dodavatele vašeho systému SIEM.

Je k dispozici prostřednictvím pomoc komunity [fórum na webu MSDN Azure Log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Toto fórum umožňuje lidem v komunitě Azure Log Integration pro podporu vzájemně otázky, odpovědi, tipy a triky. Kromě toho tým Azure Log Integration Toto fórum monitoruje a pomáhá kdykoli je to možné.

Můžete také otevřít [žádost o podporu](../azure-supportability/how-to-create-azure-support-request.md). Vyberte **integrace protokolů** jako službu, pro kterou jsou žádosti o podporu.

## <a name="next-steps"></a>Další postup
Další informace o integraci protokolů Azure najdete v tématu:

* [Integrace protokolů Microsoft Azure pro protokoly Azure](https://www.microsoft.com/download/details.aspx?id=53324): Tato stránka Download Center poskytuje podrobnosti, požadavky na systém a pokyny k instalaci pro integraci protokolů Azure.
* [Úvod do integrace protokolů Azure](security-azure-log-integration-overview.md): Tento článek vás seznámí s integrací protokolů Azure, jejích klíčových funkcích a jak to funguje.
* [Integrace protokolů Azure – nejčastější dotazy](security-azure-log-integration-faq.md): Tento článek obsahuje odpovědi na otázky týkající se integrace protokolů Azure.
* [Protokoly auditu nových funkcí pro diagnostiku Azure a Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): Tento příspěvek na blogu vás seznámí s protokoly auditování Azure a dalších funkcí, které vám pomohou získat přehled o operacích vašich prostředků Azure.
