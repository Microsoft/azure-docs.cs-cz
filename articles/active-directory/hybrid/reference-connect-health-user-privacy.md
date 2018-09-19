---
title: Azure AD Connect Health a ochrana osobních údajů uživatelů | Dokumentace Microsoftu
description: Tento dokument popisuje, ochrana osobních údajů uživatele pomocí služby Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: 7d3f625524161703b7be822c1db0fd5b21030dca
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312274"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Ochrana osobních údajů uživatelů a Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Tento článek se zabývá Azure AD Connect Health a uživatele o ochraně osobních údajů.  Informace o službě Azure AD Connect a uživatelské ochrany osobních údajů najdete v článku [tady](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Klasifikace ochrany osobních údajů uživatele
Azure AD Connect Health, které patří do **zpracovatelem** kategorie klasifikace podle nařízení GDPR. Jako zpracovatelem kanál služba poskytuje služby pro zpracování dat klíče partnerů a spotřebitelů end. Azure AD Connect Health negeneruje uživatelská data a nemá žádnou kontrolu nezávislé, jaké osobní data se shromažďují a jak se používají. Načítání dat, agregace, analýzy a generování sestav v Azure AD Connect Health jsou založeny na stávajících datech v místním prostředí. 

## <a name="data-retention-policy"></a>Zásady uchovávání dat
Azure AD Connect Health nepodporuje generování sestav, provádět analýzy ani ukázala po uplynutí 30 dnů. Azure AD Connect Health proto není ukládání, zpracování nebo zachovat žádná data se po uplynutí 30 dnů. Tento návrh je kompatibilní s nařízení GDPR, Microsoft o ochraně osobních údajů dodržování předpisů a zásad uchovávání dat služby Azure AD. 

Servery s aktivní **data služby Health service není aktuální** **chyba** výstrahy na více než 30 po sobě jdoucích dní naznačují, že žádná data bylo dosaženo Connect Health během tohoto časového intervalu. Tyto servery budou zakázané a nezobrazují na portálu Connect Health. Chcete-li znovu povolit na serverech, musíte odinstalovat a [přeinstalujte agenta stavu](how-to-connect-health-agent-install.md). Mějte prosím na paměti, že tato akce není požadována k **upozornění** pomocí výstrahy stejného typu. Upozornění označují, že částečná data ze serveru, který se zobrazí výstraha pro chybí. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Zakázat shromažďování dat a monitorování v Azure AD Connect Health
Azure AD Connect Health umožňuje ukončit shromažďování dat pro každý monitorovaný server nebo instanci monitorované služby. Můžete například zastavit shromažďování dat pro jednotlivé servery služby AD FS (Active Directory Federation Services), které jsou monitorovány pomocí služby Azure AD Connect Health. Můžete také zastavit shromažďování dat pro celou instanci služby AD FS, která je monitorována používání služby Azure AD Connect Health. Pokud budete chtít udělat, se odpovídající servery odstraní po zastavení shromažďování dat z portálu Azure AD Connect Health. 

>[!IMPORTANT]
> Potřebujete oprávnění globálního správce Azure AD nebo roli přispěvatele v RBAC pro odstranění monitorované servery z Azure AD Connect Health.
>
> Odebrání serveru nebo instance služby z Azure AD Connect Health není vrátit zpět akce. 

### <a name="what-to-expect"></a>Co můžete očekávat?
Když zastavíte shromažďování dat a monitorování pro jednotlivé monitorovaném serveru nebo instance monitorované služby, vezměte na vědomí následující:

- Po odstranění instance monitorované služby instance je odebrána ze seznamu monitorování služby Azure AD Connect Health na portálu. 
- Při odstranění monitorovaném serveru nebo instance monitorované služby agenta stavu se neodinstalují nebo odebrány z vašich serverů. Agent stavu je nakonfigurován tak, aby odesílání dat do Azure AD Connect Health. Je potřeba ručně odinstalovat agenta stavu na dříve monitorované servery.
- Pokud jste před provedením tohoto kroku agenta stavu neodinstalovali, můžou na serverech vztahujících se k agentovi stavu objevit události chyb.
- Všechna data patřící k instanci monitorované služby se odstraní podle zásad uchovávání dat Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Zakázat shromažďování dat a monitorování pro instanci monitorované služby
Zobrazit [odebrání instance služby z Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Zakázat shromažďování dat a monitorování pro monitorovaném serveru
Zobrazit [odebrání serveru z Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Zakázat shromažďování dat a monitorování pro všechny monitorované služby v Azure AD Connect Health
Také poskytuje možnost zastavit shromažďování dat z Azure AD Connect Health **všechny** registrované služby v tenantovi. Doporučujeme, abyste pečlivě promyslet a všichni globální správci úplné potvrzení před provedením akce. Po zahájení procesu služby Connect Health se zastavit příjem, zpracování a generování sestav žádná data všech služeb. Stávající data ve službě Connect Health se uchovají po dobu více než 30 dnů.
Pokud chcete zastavit shromažďování dat z konkrétní server, postupujte podle kroků uvedených v odstranění konkrétních serverů. Chcete-li zastavit shromažďování dat tenant-wise, následujícím postupem zastavit shromažďování dat a odstranit všechny služby klienta.

1.  Klikněte na **obecné nastavení** podle konfigurace v hlavním okně. 
2.  Klikněte na **zastavit shromažďování dat** tlačítko v horní části okna. Další možnosti konfigurace nastavení tenanta se deaktivuje, jakmile se spustí proces.  
 
 ![Zastavit shromažďování dat](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3.  Zkontrolujte seznam připojení služby, které jsou ovlivněny kolekce dat se zastavuje. 
4.  Zadejte název tenanta přesné povolit **odstranit** tlačítko akce
5.  Klikněte na **odstranit** k aktivaci odstranění všech služeb. Connect Health se zastavit příjem, zpracování, všechna data odeslána z vašich připojení služeb reporting. Celý proces může trvat až 24 hodin. Všimněte si, že tento krok je nevratná operace. 
6.  Po dokončení procesu se nezobrazí žádné registrované služby ve službě Connect Health více. 

 ![Po zastavení shromažďování dat](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Opětovné povolení shromažďování dat a monitorování v Azure AD Connect Health
Opětovné povolení monitorování v Azure AD Connect Health pro dříve odstraněné monitorované služby, je nutné odinstalovat a [přeinstalujte agenta stavu](how-to-connect-health-agent-install.md) ve všech serverech.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Opětovné povolení shromažďování dat a monitorování pro všechny sledované služby

Shromažďování dat tenant-Wise lze obnovit v Azure AD Connect Health. Doporučujeme, abyste pečlivě promyslet a všichni globální správci úplné potvrzení před provedením akce.

>[!IMPORTANT]
> Po 24 hodinách zakázat akce, bude k dispozici následující kroky.
> Po povolení shromažďování dat, nebudou zobrazovat žádná starší data shromážděná před prezentovaných přehledů a monitorování dat ve službě Connect Health. 

1.  Klikněte na **obecné nastavení** podle konfigurace v hlavním okně. 
2.  Klikněte na **Povolit shromažďování dat** tlačítko v horní části okna. 
 
 ![Povolení shromažďování dat](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3.  Zadejte název přesné klienta k aktivaci **povolit** tlačítko.
4.  Klikněte na **povolit** tlačítko k udělení oprávnění shromažďování dat služby Connect Health. Tato změna uplatní za chvíli. 
5.  Postupujte podle [procesu instalace](how-to-connect-health-agent-install.md) a znovu nainstalujte agenta na serveru určeného k monitorování a služby, bude k dispozici na portálu.  


## <a name="next-steps"></a>Další postup
* [Projděte si zásady Microsoft Privacy Trust Center](https://www.microsoft.com/trustcenter)
* [Azure AD Connect a ochrana osobních údajů uživatele](reference-connect-user-privacy.md)

