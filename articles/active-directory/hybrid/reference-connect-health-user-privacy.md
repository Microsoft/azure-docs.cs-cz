---
title: Azure AD Connect Health a ochrana osobních údajů uživatele | Microsoft Docs
description: Tento dokument popisuje ochranu osobních údajů uživatele pomocí Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ecdfefa79716bcc0a69063293d5a1067693a78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89182356"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Ochrana osobních údajů uživatelů a Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Tento článek se věnuje Azure AD Connect Health a ochraně osobních údajů uživatelů.  Informace o Azure AD Connect a ochraně osobních údajů uživatele najdete v [článku.](reference-connect-user-privacy.md)

## <a name="user-privacy-classification"></a>Klasifikace ochrany osobních údajů uživatelů
Azure AD Connect Health spadají do kategorie **procesor dat** klasifikace GDPR. Služba jako kanál datového procesoru poskytuje služby pro zpracování dat pro klíčové partnery a koncové zákazníky. Azure AD Connect Health negeneruje uživatelská data a nemá žádnou nezávislou kontrolu nad tím, jaké osobní údaje se shromažďují a jak se používají. Načítání dat, agregace, analýzy a vytváření sestav v Azure AD Connect Health jsou založené na existujících místních datech. 

## <a name="data-retention-policy"></a>Zásady uchovávání dat
Azure AD Connect Health negeneruje sestavy, provádějí analýzy ani neposkytují přehledy po dobu 30 dnů. Proto Azure AD Connect Health neukládá, zpracovává ani neuchovává žádná data déle než 30 dnů. Tento návrh dodržuje předpisy GDPR, nařízení o dodržování předpisů Microsoftu a zásady uchovávání dat v Azure AD. 

Servery s aktivními **daty služby Health Service nejsou aktuálními** výstrahami o **chybách** po dobu více než 30 po sobě jdoucích dnů, takže v tomto časovém intervalu nedosáhnou žádné údaje o stavu připojení. Tyto servery budou zakázané a nebudou se zobrazovat na portálu Connect Health. Chcete-li znovu povolit servery, musíte odinstalovat a [znovu nainstalovat agenta stavu](how-to-connect-health-agent-install.md). Upozorňujeme, že tento postup se nevztahuje na **Upozornění** se stejným typem výstrahy. Upozornění označují, že v serveru, pro který jste upozorňováni, chybí částečně data. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Zakázat shromažďování a monitorování dat v Azure AD Connect Health
Azure AD Connect Health vám umožní zastavit shromažďování dat pro jednotlivé monitorované servery nebo pro instanci monitorované služby. Můžete například zastavit shromažďování dat pro jednotlivé servery ADFS (Active Directory Federation Services (AD FS)), které jsou monitorovány pomocí Azure AD Connect Health. Můžete také zastavit shromažďování dat pro celou instanci služby AD FS, která je monitorována pomocí Azure AD Connect Health. Pokud se rozhodnete tak učinit, budou se po zastavení shromažďování dat na portálu Azure AD Connect Health odstranit odpovídající servery. 

>[!IMPORTANT]
> Abyste mohli odstranit monitorované servery z Azure AD Connect Health, potřebujete buď oprávnění globálního správce služby Azure AD, nebo roli Přispěvatel v Azure RBAC.
>
> Odebrání instance serveru nebo služby z Azure AD Connect Health není akce vratné. 

### <a name="what-to-expect"></a>Co očekávat?
Pokud zastavíte shromažďování a monitorování dat pro jednotlivý monitorovaný Server nebo instanci monitorované služby, vezměte na vědomí následující:

- Když odstraníte instanci monitorované služby, instance se odebere ze seznamu Azure AD Connect Health služby monitorování na portálu. 
- Po odstranění monitorovaného serveru nebo instance monitorovaných služeb se Agent stavu neodinstaluje ani neodebere z vašich serverů. Agent stavu je nakonfigurován tak, aby neodesílal data do Azure AD Connect Health. Musíte ručně odinstalovat agenta stavu na dříve monitorovaných serverech.
- Pokud jste neinstalovali agenta stavu před provedením tohoto kroku, můžete zobrazit chybové události na serverech, které souvisejí s agentem stavu.
- Všechna data patřící do instance monitorované služby se odstraní podle Microsoft Azure zásady uchovávání dat.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Zakáže shromažďování a monitorování dat pro instanci monitorované služby.
Přečtěte si téma [Jak odebrat instanci služby z Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Zakázání shromažďování a monitorování dat pro monitorovaný Server
Přečtěte si téma [Jak odebrat server z Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Zakáže shromažďování a monitorování dat pro všechny monitorované služby v Azure AD Connect Health
Azure AD Connect Health taky nabízí možnost zastavit shromažďování dat **všech** registrovaných služeb v tenantovi. Před provedením této akce doporučujeme pečlivě zvážit a úplné potvrzení všech globálních správců. Po zahájení procesu připojení služby Health Service přestane přijímat, zpracovávat a nahlásit všechna vaše data služeb. Stávající data ve službě Connect Health se budou uchovávat po dobu maximálně 30 dnů.
Pokud chcete zastavit shromažďování dat pro konkrétní server, postupujte prosím podle kroků při odstraňování konkrétních serverů. Chcete-li zastavit shromažďování dat z tenanta, postupujte podle následujících kroků a zastavte shromažďování dat a odstraňte všechny služby klienta.

1. V hlavním okně klikněte na **Obecná nastavení** v části konfigurace. 
2. V horní části okna klikněte na tlačítko **Zastavit shromažďování dat** . Další možnosti nastavení konfigurace tenanta budou po spuštění procesu zakázané.  
 
   ![Zastavit shromažďování dat](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Zajistěte, aby seznam integrovaných služeb byl ovlivněn zastavením kolekcí dat. 
4. Zadejte přesný název tenanta, aby se povolilo tlačítko **Odstranit** akci.
5. Kliknutím na **Odstranit** aktivujte odstraňování všech služeb. Connect Health přestane přijímat, zpracovávat a nahlásit všechna data odesílaná z vašich integrovaných služeb. Celý proces může trvat až 24 hodin. Všimněte si, že tento krok není vratný. 
6. Až se proces dokončí, nezobrazí se žádné registrované služby v části Connect Health. 

   ![Po zastavení shromažďování dat](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Opětovné povolení shromažďování a monitorování dat v Azure AD Connect Health
Pokud chcete znovu povolit monitorování v Azure AD Connect Health pro dříve odstraněnou monitorovanou službu, musíte na všech serverech odinstalovat a [znovu nainstalovat agenta stavu](how-to-connect-health-agent-install.md) .

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Znovu povolit shromažďování a monitorování dat pro všechny monitorované služby

Shromažďování dat pro tenanta se dá obnovit v Azure AD Connect Health. Před provedením této akce doporučujeme pečlivě zvážit a úplné potvrzení všech globálních správců.

>[!IMPORTANT]
> Následující kroky budou k dispozici po 24 hodinách akce zakázání.
> Po povolení shromažďování dat se uvedená data o sledování a monitorování v části Connect Health nezobrazí žádná starší data shromážděná před. 

1. V hlavním okně klikněte na **Obecná nastavení** v části konfigurace. 
2. V horní části okna klikněte na tlačítko **Povolit shromažďování dat** . 
 
   ![Povolení shromažďování dat](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Chcete-li aktivovat tlačítko **Povolit** , zadejte přesný název tenanta.
4. Kliknutím na tlačítko **Povolit** udělíte oprávnění ke shromažďování dat ve službě Connect Health. Tato změna se použije krátce. 
5. Pomocí [instalačního procesu](how-to-connect-health-agent-install.md) přeinstalujte agenta na serverech, které se mají monitorovat, a služby budou na portálu k dispozici.  


## <a name="next-steps"></a>Další kroky
* [Přečtěte si téma zásady ochrany osobních údajů Microsoftu na webu Trust Center.](https://www.microsoft.com/trustcenter)
* [Azure AD Connect a ochrana osobních údajů uživatele](reference-connect-user-privacy.md)

