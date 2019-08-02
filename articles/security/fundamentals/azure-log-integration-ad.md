---
title: Azure Log Integration s protokoly auditu Azure Active Directory | Microsoft Docs
description: Naučte se instalovat službu Azure Log Integration a integrovat protokoly z protokolů auditu Azure.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 0820ca227a4d0e8c71ed3f35cd8fa2841163d38f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727649"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrace protokolů auditu Azure Active Directory

Události auditu Azure Active Directory (Azure AD) vám pomůžou identifikovat privilegované akce, k nimž došlo v Azure Active Directory. Typy událostí, které můžete sledovat, můžete zobrazit zkontrolováním [Azure Active Directory události sestav auditu](../../active-directory/reports-monitoring/concept-audit-logs.md).


>[!IMPORTANT]
> Funkce integrace protokolu Azure bude zastaralá od 06/15/2019. Stahování AzLog bylo zakázáno 27. června 2018. Další informace o tom, co dělat, najdete v příspěvku [pomocí Azure monitoru pro integraci s nástroji Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) . 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Postup integrace protokolů auditu Azure Active Directory

> [!NOTE]
> Než se pokusíte o kroky v tomto článku, musíte si projít [článek Začínáme](azure-log-integration-get-started.md) a provést příslušné kroky tam.

1. Otevřete příkazový řádek a spusťte tento příkaz:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Spusťte tento příkaz: 
 
   ``azlog createazureid``

   Tento příkaz vás vyzve k přihlášení do Azure. Příkaz potom vytvoří Azure Active Directory instančního objektu v klientech Azure AD, kteří jsou hostiteli předplatných Azure, ve kterých je přihlášený uživatel správcem, spolusprávcem nebo vlastníkem. Pokud je přihlášený uživatel jenom uživatel typu Host v tenantovi Azure AD, příkaz se nezdaří. Ověřování do Azure se provádí prostřednictvím služby Azure AD. Vytvoření instančního objektu pro Azure Log Integration vytvoří identitu služby Azure AD, která má přístup ke čtení z předplatných Azure.

3. Spuštěním následujícího příkazu zadejte ID tenanta. Abyste mohli příkaz Spustit, musíte být členem role správce tenanta.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Příklad:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Zkontrolujte následující složky a potvrďte, že se v nich vytvoří soubory JSON protokolu auditu Azure Active Directory:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Následující video ukazuje postup popsaný v tomto článku:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Konkrétní pokyny k přenosu informací do souborů JSON do systému správy událostí a informací o zabezpečení (SIEM) získáte od dodavatele SIEM.

Pomoc komunity je k dispozici prostřednictvím [Azure log Integration fóra MSDN](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Tato Fórum umožňuje lidem v Azure Log Integration komunitě podporovat otázky, odpovědi, tipy a triky. Kromě toho tým Azure Log Integration monitoruje toto fórum a pomůže vám to pokaždé, když to může.

Můžete také otevřít [žádost o podporu](../../azure-supportability/how-to-create-azure-support-request.md). Jako službu, pro kterou požadujete podporu, vyberte **integrace protokolu** .

## <a name="next-steps"></a>Další postup
Další informace o Azure Log Integration najdete v těchto tématech:

* [Microsoft Azure log Integration pro protokoly Azure](https://www.microsoft.com/download/details.aspx?id=53324): Tato stránka služby Stažení softwaru obsahuje podrobnosti, požadavky na systém a pokyny k instalaci Azure Log Integration.
* [Úvod do Azure log Integration](azure-log-integration-overview.md): Tento článek vás seznámí s Azure Log Integration, jeho klíčovými schopnostmi a jak funguje.
* [Nejčastější dotazy k Azure log Integration](azure-log-integration-faq.md): Tento článek obsahuje odpovědi na otázky týkající se Azure Log Integration.
* [Nové funkce pro Azure Diagnostics a protokoly auditu Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): Tento Blogový příspěvek vás seznámí s protokoly auditu Azure a dalšími funkcemi, které vám pomůžou získat přehled o operacích vašich prostředků Azure.
