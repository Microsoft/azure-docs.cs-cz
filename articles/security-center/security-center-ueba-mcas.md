---
title: Detekce ve službě Azure Security Center hrozeb | Dokumentace Microsoftu
description: " Detekce hrozeb a škodlivý díky integraci Microsoft Cloud App Security s Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: 7ffb9684045031c5bca7a79a15db7cb16fc99e9b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108003"
---
# <a name="ueba-for-azure-resources-and-users"></a>UEBA pro uživatele a prostředky Azure 

Partneři Azure Security Center pomocí Microsoft Cloud App Security, aby vám oznámení na základě uživatele a entity behaviorální analýzy (Behavioral) pro vaše prostředky Azure a uživatele (aktivit Azure). Tyto výstrahy zjišťovat anomálie v chování uživatelů a jsou založené na uživatele a entity pro vypracování analýzy chování a machine learning (ML) tak, aby ihned spustíte rozšířeného zjišťování hrozeb napříč aktivity vašich předplatných. Protože jsou automaticky povolené, nové detekce anomálií poskytnout okamžitou výsledky tím, že poskytuje okamžitou detekce, cílení na mnoho anomálií chování uživatelů a prostředcích přidružených k vašemu předplatnému. Tyto výstrahy navíc využít další data, která již existuje v modulu detekce Microsoft Cloud App Security, které vám pomohou zrychlit proces šetření a obsahovat trvalých hrozeb. 

> [!NOTE]
> Azure Security Center, která může ukládat kopie související se zabezpečením zákaznická data, shromážděná z nebo související s prostředkem zákazníka (např. virtuální počítač nebo Azure Active Directory tenanta): (a) ve stejné zeměpisné oblasti jako prostředek, s výjimkou těchto zeměpisné oblasti kde Microsoft ještě musíme nasazení Azure Security Center, ve kterém se uloží případ kopii těchto dat v USA. a (b) Pokud Azure Security Center používá ke zpracování těchto dat jiné Online službu Microsoftu, může ukládat taková data v souladu s pravidly informace o zeměpisné poloze tuto Online službu.
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Požadavky

- Platný, aktivuje [licenci Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
- [Security Center úrovně Standard](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>Výstrahy detekce hrozeb

Security Center podporuje výstrahy detekce anomálií Cloud App Security, jako například:

**Neuskutečnitelná cesta**
-  Tato detekce identifikuje dvě aktivity uživatele (je jeden nebo více relací) pocházející z geograficky vzdáleném umístění v časovém období kratší než čas ho by trvalo cestují z první umístění do druhého, která uživateli že je jiný uživatel pomocí stejných přihlašovacích údajů. Toto zjišťování využívá strojové učení algoritmu, který ignoruje zřejmé "počet falešně pozitivních výsledků" přispívání do podmínky neuskutečnitelné cesty, jako je například sítě VPN a umístění pravidelně používat další uživatelé v organizaci. Detekce má období učení sedm dní, během kterých se naučí vzor aktivit nového uživatele.

**Aktivita z málo časté země**
- Tato detekce ohledem na minulou umístění aktivit k určení nové a úlohy s řídkým umístění. Modul detekce anomálií ukládá informace o předchozí umístění, které používají uživatelé v organizaci. Výstraha se aktivuje, dojde-li aktivita z umístění, ke kterému nedávno nebo nikdy nenavštívil žádný uživatel v organizaci. 

**Aktivita z anonymních IP adres**
- Tato detekce identifikuje, že uživatelé jsou aktivní z adresy IP, která se identifikovala jako IP adresa anonymního proxy serveru. Tato proxy používají lidé, kteří mají skrýt IP adresu svého zařízení a může sloužit ke škodlivým činnostem. Toto zjišťování využívá strojové učení algoritmu, který snižuje "falešně pozitivní výsledky", jako jsou správně označené IP adresy, které se běžně používají uživatelé v organizaci.
 
  ![Výstrahy detekce hrozeb](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>Vypnutí výstrahy detekce hrozeb

Tyto výstrahy jsou ve výchozím nastavení povolené, ale lze je vypnout:

1. V okně Security Center vyberte **zásady zabezpečení**. Předplatné, které chcete změnit, klikněte na tlačítko **upravit nastavení**.
2.  Klikněte na tlačítko **detekce hrozeb**.
3. V části **povolovat integrace**, zrušte zaškrtnutí políčka **povolit Microsoft Cloud App Security přístup k data**a klikněte na tlačítko **Uložit**.

   ![Výstrahy detekce hrozeb](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> Existuje sedm dní, během které všechny anomálií se generují výstrahy detekce období učení. Po tomto období se každá relace porovnává s aktivitou, dobou aktivity uživatelů, IP adresami, zařízeními a dalšími parametry zjištěnými za poslední měsíc a skórem rizika těchto aktivit. Tyto detekce jsou součástí sady machine learningu modul detekce anomálií této profily prostředí a aktivačních událostí výstrahy s ohledem na standardních hodnot, které byly získány v aktivitě vaší organizace. Tyto detekce také využívají algoritmy strojového učení navržené tak, aby profil uživatele a protokolů na vzor snížil počet falešných poplachů.
>
  
## <a name="next-steps"></a>Další postup
V tomto článku jsme si ukázali, abyste pracovali s detekcí hrozeb ve službě Azure Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
