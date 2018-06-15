---
title: Použití Azure Security Center doporučení k vylepšení zabezpečení | Microsoft Docs
description: " Další informace o použití zásady a doporučení zabezpečení v Azure Security Center můžete zmírnit útok na zabezpečení. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: terrylan
ms.openlocfilehash: 0616f5e501324bfd821c1455ce234602f1fcf1bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866664"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Použití Azure Security Center doporučení k vylepšení zabezpečení
Konfigurace zásad zabezpečení a pak implementace doporučení pomocí služby Azure Security Center můžete snížit pravděpodobnost událostí významné zabezpečení. Tento článek ukazuje, jak používat zásady a doporučení zabezpečení v Centru zabezpečení můžete zmírnit útok na zabezpečení.

> [!NOTE]
> Tento článek vychází role a koncepty představené v Centru zabezpečení [Průvodce plánováním a operace](security-center-planning-and-operations-guide.md). Je vhodné před pokračováním si prohlédněte v příručce plánování.
>
>

## <a name="managing-security-recommendations"></a>Správa doporučení zabezpečení
Zásady zabezpečení definuje sadu ovládacích prvků, které se doporučují pro prostředky v rámci zadané předplatné nebo skupinu prostředků. V Security Center určíte zásady podle požadavků zabezpečení vaší společnosti. Další informace najdete v tématu [nastavovat zásady zabezpečení ve službě Security Center](security-center-policies.md).

Zásady zabezpečení pro skupiny prostředků se dědí z úrovně předplatného.

![Dědičnost zásad zabezpečení][1]

Pokud potřebujete vlastní zásady v určitých skupinách prostředků, můžete zakázat dědičnost ve skupině prostředků. Zakázat, nastavte dědičnost na jedinečný okno Zásady zabezpečení a ovládacích prvků, které Security Center zobrazuje doporučení pro přizpůsobení.

Například pokud máte úlohy, které nevyžadují zásadu SQL databáze transparentní dat šifrování (TDE), vypněte tuto zásadu na úrovni předplatného a povolte ji jenom ve skupinách prostředků, ve kterých je požadovaná.

> [!NOTE]
> V případě konfliktu mezi zásadou na úrovni předplatného a zásadou na úrovni skupiny prostředků má přednost zásada na úrovni skupiny prostředků.
>
>

Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se v ovládacích prvcích nastavení v zásadě zabezpečení na základě doporučení. Doporučení vás provede procesem konfigurace potřebných zabezpečení ovládací prvky.

Aktuální zásady doporučení v Security Center se zaměřují na aktualizací systému, konfigurace operačního systému, skupin zabezpečení podsítí a virtuálních počítačů (VM), auditování databáze SQL TDE databáze SQL, sítě a brány firewall webových aplikací. Nejaktuálnější pokrytí doporučení služby Security Center, najdete v části [Správa doporučení zabezpečení v Centru zabezpečení](security-center-recommendations.md).

## <a name="scenario"></a>Scénář
Tento scénář popisuje, jak používat Security Center Pokud chcete snížit pravděpodobnost významné bezpečnostního incidentu monitorování doporučení služby Security Center a akce. Tento scénář používá fiktivní společnosti, Contoso a rolí, které jsou uvedené v Centru zabezpečení [Průvodce plánováním a operace](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). Role představují jednotlivce a týmy, které mohou používat Security Center při provádění různých úloh souvisejících se zabezpečením. Role jsou:

![Scénář role][2]

Contoso nedávno migrace některé z jejich místních prostředků do Azure. Contoso chce implementovat a udržovat ochranu, která snižují jejich zranitelnost vůči útoku zabezpečení svých prostředků v cloudu.

## <a name="recommended-solution"></a>Doporučené řešení
Řešením je použití Security Center pro zabránění a zjištění chyb zabezpečení. Contoso má přístup k Security Center prostřednictvím svého předplatného Azure. [Úroveň Free](security-center-pricing.md) služby Security Center je automaticky povolené na všechny odběry služby Azure a shromažďování dat je povolené na všech virtuálních počítačích v rámci svého předplatného.

Nakonfiguruje David v rámci zabezpečení IT společnosti Contoso **zásady zabezpečení** pomocí Security Center. Security Center analyzuje stav zabezpečení prostředků Azure společnosti Contoso. Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří **doporučení** podle nastavení v zásadě zabezpečení ovládacích prvků.

Jeff, vlastník úloh v cloudu, je zodpovědný za implementaci a udržování ochrany v souladu se zásadami zabezpečení společnosti Contoso. Jeff můžete monitorovat doporučení vytvořené Centrum zabezpečení pro použití ochrany. Doporučení Jeff provede procesem konfigurace potřebných zabezpečení ovládací prvky.

Aby Jan k implementaci a udržování ochrany a odstranění ohrožení zabezpečení potřebné informace pro:

- Monitorování doporučení zabezpečení pomocí služby Security Center
- Vyhodnocení doporučení zabezpečení a rozhodnout, pokud mu použít nebo vymazat
- Použít doporučení zabezpečení

Pojďme postupujte podle kroků Jeff zobrazíte, jak pomocí doporučení služby Security Center mu provede procesem konfigurace ovládacích prvků k vyloučení chyb zabezpečení.

## <a name="how-to-implement-this-solution"></a>Postup implementace tohoto řešení
Jeff přihlásí [portál Azure](https://azure.microsoft.com/features/azure-portal/) a otevře se konzole Security Center. V rámci své každodenní aktivity sledování se mu zkontroluje, pokud jsou doporučení zabezpečení tak, že provedete následující kroky:

1. Jan vybere **doporučení** dlaždici otevřete **doporučení**.
   ![Vyberte dlaždici doporučení][3]
2. Jan zkontroluje seznam doporučení. Zjistí, že Security Center poskytl seznam doporučení v pořadí podle priority z nejvyšší prioritu s nejnižší prioritou. Rozhodne se adresa s vysokou prioritou doporučení v seznamu. Vybere **nainstalovat službu Endpoint Protection** pod **doporučení**.
3. **Nainstalovat službu Endpoint Protection** otevře zobrazení seznamu virtuálních počítačů bez antimalwarových povolena. Jan zkontroluje seznam virtuálních počítačů, vybere všechny virtuální počítače a potom vybere **nainstalovat na virtuálních počítačích 3**.
   ![Instalace Endpoint Protection][4]
4. **Vyberte Endpoint Protection** otevře Jeff poskytování dvě antimalwarových řešení. Jan vybere **Antimalware od Microsoftu** řešení.
5. Zobrazí se další informace o antimalwarové řešení. Jan vybere **vytvořit**.
   ![Antimalware od Microsoftu][5]
6. Jan zadá požadovaná konfigurační nastavení v části **nainstalovat** a vybere **OK**.

[Microsoft Antimalware](../security/azure-security-antimalware.md) je teď aktivní na vybrané virtuální počítače.

Jeff nadále pohyb s vysokou prioritou a doporučení se střední prioritou, při rozhodování o provádění. Jeff odkazuje [Správa doporučení zabezpečení](security-center-recommendations.md) článek vám pomůže porozumět doporučení a co každé z nich dělá pokud mu použije ji.

Jeff zjišťuje, které [Microsoft Security Response Center (MSRC)](../security/azure-security-response-center.md) provádí sledování zabezpečení vyberte síť Azure a infrastruktury a přijímá stížností intelligence a zneužití ohrožení od jiných výrobců. Pokud Jeff poskytuje zabezpečení kontaktní údaje pro předplatné Azure společnosti Contoso, kontakty Microsoft Contoso, pokud střediska MSRC zjišťuje data zákazníků společnosti Contoso přístupem k nezákonné nebo neoprávněným strana. Pojďme postupujte Jeff podle mu se vztahuje **zadejte kontaktní údaje zabezpečení** doporučení (má tyto požadavky se závažností Střední seznam doporučení výše).

1. Jan vybere **zadejte kontaktní údaje zabezpečení** pod **doporučení**, otevře **zadejte kontaktní údaje zabezpečení**.
2. Jan vybere předplatné Azure poskytuje kontaktní informace. Druhý **zadejte kontaktní údaje zabezpečení** otevře se okno.
   ![Kontaktní údaje zabezpečení][6]
3. V části **zadejte kontaktní údaje zabezpečení**, zadá Jeff:

  - zabezpečení kontaktní e-mailové adresy oddělené čárkami (není limit počtu e-mailové adresy, které může zadat)
  - telefonní číslo kontaktu jeden zabezpečení

4. Jan také zapne možnost **zaslat mi e-maily o výstrahách** příjmu e-mailů o výstrahy s vysokou závažností.
5. Jan vybere **OK** použít kontaktní informace zabezpečení do předplatného společnosti Contoso.

Nakonec Jeff zkontroluje doporučení s nízkou prioritou **ohrožení zabezpečení operačního systému opravit** a určuje, že toto doporučení se nedá použít. Chce zavřít doporučení. Jan vybere se třemi tečkami, které se zobrazí vpravo a pak vybere **přeskočení**.
   ![Zavření doporučení][7]

## <a name="conclusion"></a>Závěr
Monitorování doporučení ve službě Security Center vám mohou pomoci eliminovat ohrožení zabezpečení, než dojde k útoku. Můžete-li zabránit bezpečnostního incidentu, implementaci a udržování ochrany zásadám zabezpečení ve službě Security Center.

## <a name="next-steps"></a>Další kroky
Tento scénář ukázal, jak používat zásady a doporučení zabezpečení v Centru zabezpečení můžete zmírnit útok na zabezpečení. Najdete v článku [reakcí na incidenty scénář](security-center-incident-response.md) se dozvíte, jak získat odpověď incidentu plán před útokem probíhá.

Další informace o službě Security Center najdete v tématu:

* [Sledování stavu zabezpečení](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reakce na výstrahy zabezpečení.
* [Monitorování a zpracování události zabezpečení](security-center-events-dashboard.md) – zjistěte, jak sledovat a shromažďovat události zabezpečení procesu v čase.
* [Sledování partnerských řešení](security-center-partner-solutions.md) – zjistěte, jak sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější informace zabezpečení Azure a informace.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png
