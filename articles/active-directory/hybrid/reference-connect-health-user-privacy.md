---
title: Azure AD Connect stav a ochrana osobních údajů uživatelů | Dokumenty společnosti Microsoft
description: Tento dokument popisuje ochranu osobních údajů uživatelů se službou Azure AD Connect Health.
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
ms.openlocfilehash: 58cddcde71e6e86b9abe07eb4200f13ad55ea659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253660"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Ochrana osobních údajů uživatelů a stav Služby Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Tento článek se zabývá Azure AD Connect health a soukromí uživatelů.  Informace o azure ad připojení a soukromí uživatelů naleznete v článku [zde](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Klasifikace ochrany osobních údajů uživatelů
Azure AD Connect Health spadá do kategorie **procesoru dat** klasifikace GDPR. Jako kanál zpracovatele dat poskytuje služba služby zpracování dat klíčovým partnerům a koncovým spotřebitelům. Azure AD Connect Health negeneruje uživatelská data a nemá žádnou nezávislou kontrolu nad tím, jaká osobní data se shromažďují a jak se používají. Načítání dat, agregace, analýza a vytváření sestav ve službě Azure AD Connect Health jsou založeny na existujících místních datech. 

## <a name="data-retention-policy"></a>Zásady uchovávání dat
Azure AD Connect Health negeneruje sestavy, neprovádí analýzy ani neposkytuje přehledy po 30 dnech. Azure AD Connect Health tedy neukládá, nezpracovává ani neuchovává žádná data po dobu 30 dnů. Tento návrh je v souladu s předpisy GDPR, předpisy pro dodržování předpisů microsoftu o ochraně osobních údajů a zásadami uchovávání dat azure ad. 

Servery s **aktivními daty služby Health nejsou aktuální** upozornění na **chyby** po dobu více než 30 po sobě jdoucích dnů naznačují, že během tohoto časového období nedorazila žádná data k připojení stavu. Tyto servery budou zakázány a nebudou zobrazeny na portálu Connect Health. Chcete-li servery znovu povolit, je nutné odinstalovat a [znovu nainstalovat agenta stavu](how-to-connect-health-agent-install.md). Upozorňujeme, že toto se nevztahuje na **upozornění** se stejným typem výstrahy. Upozornění označují, že na serveru, na který jste upozorněni, chybí částečná data. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Zakázání shromažďování a monitorování dat ve službě Azure AD Connect Health
Azure AD Connect Health umožňuje zastavit shromažďování dat pro každý jednotlivý monitorovaný server nebo pro instanci monitorované služby. Můžete například zastavit shromažďování dat pro jednotlivé servery ADFS (ADFS( Active Directory Federation Services), které jsou monitorovány pomocí služby Azure AD Connect Health. Můžete také zastavit shromažďování dat pro celou instanci ADFS, která se monitoruje pomocí Azure AD Connect Health. Pokud se rozhodnete tak učinit, odpovídající servery se odstraní z portálu Azure AD Connect Health po zastavení shromažďování dat. 

>[!IMPORTANT]
> K odstranění monitorovaných serverů ze služby Azure AD Connect Health potřebujete oprávnění globálního správce služby Azure AD nebo roli přispěvatele v rbacu.
>
> Odebrání instance serveru nebo služby ze stavu Azure AD Connect není reverzibilní akce. 

### <a name="what-to-expect"></a>Co můžete očekávat?
Pokud zastavíte shromažďování a monitorování dat pro jednotlivý monitorovaný server nebo instanci monitorované služby, poznamenejte si následující:

- Když odstraníte instanci monitorované služby, instance se odebere ze seznamu monitorovacích služeb Azure AD Connect Health na portálu. 
- Odstraníte-li monitorovaný server nebo instanci monitorované služby, nebude agent stavu odinstalován nebo odebrán ze serverů. Agent stavu je nakonfigurovaný tak, aby neodesílal data do služby Azure AD Connect Health. Agenta stavu je třeba ručně odinstalovat na dříve monitorovaných serverech.
- Pokud jste neodinstalovali agenta stavu před provedením tohoto kroku, může se zobrazit chybové události na serveru (y) vztahující se k agenta stavu.
- Všechna data, která patří do instance monitorované služby, se odstraní podle zásad uchovávání dat Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Zakázání shromažďování a monitorování dat pro instanci monitorované služby
Přečtěte [si, jak odebrat instanci služby z Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Zakázání shromažďování a monitorování dat pro monitorovaný server
Přečtěte [si, jak odebrat server ze služby Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Zakázání shromažďování a monitorování dat pro všechny monitorované služby ve službě Azure AD Connect Health
Azure AD Connect Health také poskytuje možnost zastavit shromažďování dat **všech** registrovaných služeb v tenantovi. Před přijetím akce doporučujeme pečlivě zvážit a plně potvrdit všechny globální správce. Jakmile proces začne, služba Connect Health přestane přijímat, zpracovávat a vykazovat všechna data všech vašich služeb. Stávající data ve službě Connect Health budou uchovávána maximálně 30 dní.
Chcete-li zastavit shromažďování dat konkrétního serveru, postupujte podle pokynů při odstranění konkrétních serverů. Chcete-li zastavit shromažďování dat z hlediska klienta, ukončit shromažďování dat a odstranit všechny služby klienta.

1. Klikněte na **Obecné nastavení** v konfiguraci v hlavním noži. 
2. Klikněte na tlačítko **Zastavit sběr dat** v horní části okna. Ostatní možnosti nastavení konfigurace klienta budou zakázány po spuštění procesu.  
 
   ![Zastavit shromažďování dat](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Zajistěte seznam palubních služeb, které jsou ovlivněny zastavením shromažďování údajů. 
4. Zadáním přesného názvu klienta povolíte tlačítko **Akce Odstranit.**
5. Kliknutím na **Odstranit** spustíte odstranění všech služeb. Connect Health přestane přijímat, zpracovávat a hlásit všechna data odeslaná z vašich palubních služeb. Celý proces může trvat až 24 hodin. Všimněte si, že tento krok není reverzibilní. 
6. Po dokončení procesu se již v programu Connect Health nezobrazí žádné registrované služby. 

   ![Po zastavení sběru dat](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Opětovné povolení shromažďování a monitorování dat ve službě Azure AD Connect Health
Chcete-li znovu povolit monitorování ve službě Azure AD Connect Health pro dříve odstraněnou monitorovnou službu, musíte odinstalovat a [znovu nainstalovat agenta stavu](how-to-connect-health-agent-install.md) na všech serverech.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Opětovné povolení sběru a monitorování dat pro všechny monitorované služby

Shromažďování dat z hlediska tenanta lze obnovit ve službě Azure AD Connect Health. Před přijetím akce doporučujeme pečlivě zvážit a plně potvrdit všechny globální správce.

>[!IMPORTANT]
> Následující kroky budou k dispozici po 24 hodinách akce zakázání.
> Po povolení shromažďování dat se v prezentovaných datech a datech monitorování v programu Connect Health nezobrazí žádná starší data shromážděná dříve. 

1. Klikněte na **Obecné nastavení** v konfiguraci v hlavním noži. 
2. Klikněte na **tlačítko Povolit sběr dat** v horní části okna. 
 
   ![Povolení shromažďování dat](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Zadejte přesný název klienta pro aktivaci tlačítka **Povolit.**
4. Kliknutím na **tlačítko Povolit** udělíte oprávnění ke shromažďování dat ve službě Connect Health. Změna bude brzy uplatněna. 
5. Podle [instalačního procesu](how-to-connect-health-agent-install.md) přeinstalujte agenta na serverech, které mají být monitorovány, a služby budou k dispozici na portálu.  


## <a name="next-steps"></a>Další kroky
* [Projděte si zásady ochrany osobních údajů společnosti Microsoft v Centru zabezpečení](https://www.microsoft.com/trustcenter)
* [Azure AD Connect a ochrana osobních údajů uživatelů](reference-connect-user-privacy.md)

