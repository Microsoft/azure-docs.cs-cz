---
title: Vytvoření snímku sestavy Cloud App Discovery v Azure Active Directory | Microsoft Docs
description: Poskytuje informace o hledání a Správa aplikací pomocí Cloud App Discovery, jaké jsou výhody a jak to funguje.
services: active-directory
keywords: cloud app discovery,. Správa aplikací
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/22/2017
ms.author: curtand
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: ad4591223c72893a4488f5515d8ceb83e0d7f8cf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-cloud-app-discovery-snapshot-reports"></a>Vytvoření snímku sestavy Cloud App Discovery

Před nastavením kolektor protokolů automatické, nahrání protokolu ručně a nechat ji analyzovat Cloud App Security. Pokud ještě nemáte protokolu a chcete zobrazit ukázkové jak by měla vypadat protokolu, pomocí níže uvedeného postupu stáhnout ukázkový soubor protokolu zobrazíte co protokolu by měla vypadat.

## <a name="to-create-a-snapshot-report"></a>Pro vytvoření snímku sestavy

1. Shromážděte soubory protokolů z vaší brány firewall a proxy server, pomocí kterého se uživatelé ve vaší organizaci přístup k Internetu. Shromážděte protokoly v době nejsilnějšího provozu, které jsou zástupce aktivity uživatelů ve vaší organizaci.
2. Na [panelu nabídek Cloud App Security](https://portal.cloudappsecurity.com), vyberte **Discover**a potom **vytvoření snímku sestavy**.
  
  ![Vytvořit novou sestavu snímku](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-command.png)
3. Zadejte **název sestavy** a **popis**.
    
  ![Nové snímku sestavy](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-form.png)
4. Vyberte **zdroj dat** ze kterého chcete odeslat soubory protokolu.
5. Zkontrolujte formát a ujistěte se, že je správně naformátovaný podle ukázku, kterou si můžete stáhnout. Klikněte na tlačítko **zobrazení a ověření** a pak klikněte na **stáhnout ukázkový protokol**. Potom porovnejte svůj protokol s ukázkou poskytované Ujistěte se, že je kompatibilní.
  
  ![Ověřit formát protokolu](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-verify.png)
  >  [!NOTE]
  > Ukázka formátu FTP je podporován v snímky a automatizované nahrávání při syslog je podporována v pouze automatického odesílání. Stahování ukázkový protokol stáhne ukázkový protokol FTP.
6. **Zvolte protokolů přenosů** , kterou chcete nahrát. Najednou můžete nahrát až 20 souborů. Komprimované a komprimované soubory jsou také podporovány.
  
7. Klikněte na možnost **Vytvořit**. Po dokončení nahrávání, může trvat nějakou dobu jejich rozložení a analýza. Pokud ano, Cloud App Discovery pošle e-mailové oznámení, když jsou připravené.

8. Vyberte **spravovat snímek sestavy** a vyberte položku snímek sestavy.
  
  ![Správa sestav snímku](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-manage.png)

## <a name="next-steps"></a>Další postup

* [Začít používat Cloud App Discovery ve službě Azure AD](cloudappdiscovery-get-started.md)
* [Konfigurace automatického protokolu nahrávání průběžné vytváření sestav](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Použití vlastního analyzátoru protokolů](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
