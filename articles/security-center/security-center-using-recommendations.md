---
title: Použití služby Azure Security Center doporučení k vylepšení zabezpečení | Dokumentace Microsoftu
description: " Další informace o použití zásady a doporučení zabezpečení v Azure Security Center můžete zmírnit útok na zabezpečení. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: terrylan
ms.openlocfilehash: 978ef8087665edb3f0bfaa0bd0a55f4d8ab9a12d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251498"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Použití služby Azure Security Center doporučení k vylepšení zabezpečení
Tak, že konfigurace zásad zabezpečení a pak implementace doporučení poskytovaných službou Azure Security Center můžete snížit riziko zabezpečení významné události. V tomto článku se dozvíte, jak používat zásady zabezpečení a doporučení ve službě Security Center můžete zmírnit útok na zabezpečení.

> [!NOTE]
> Tento článek vychází z role a konceptům uvedeným ve službě Security Center [Průvodce plánováním a provozem](security-center-planning-and-operations-guide.md). Je vhodné zkontrolovat příručky plánování před pokračováním.
>
>

## <a name="managing-security-recommendations"></a>Správa doporučení zabezpečení
Zásady zabezpečení definují sadu ovládacích prvků, které se doporučují pro prostředky v rámci daného předplatného nebo skupiny prostředků. Ve službě Security Center určíte zásady podle požadavků zabezpečení vaší společnosti. Další informace najdete v tématu [nastavení zásad zabezpečení ve službě Security Center](security-center-policies.md).

Zásady zabezpečení pro skupiny prostředků se dědí z úrovně předplatného.

![Dědičnost zásad zabezpečení][1]

Pokud potřebujete vlastní zásady v určitých skupinách prostředků, můžete zakázat dědičnost ve skupině prostředků. Zakázat, nastavte dědičnosti na jedinečné v okně Zásady zabezpečení a upravit ovládací prvky, které Security Center zobrazí doporučení pro.

Například pokud máte úlohy, které nevyžadují zásadu SQL Database transparentní šifrování dat (TDE), vypněte tuto zásadu na úrovni předplatného a povolit pouze ve skupinách prostředků, ve kterém jsou vyžadována transparentní šifrování dat SQL.

> [!NOTE]
> V případě konfliktu mezi zásadou na úrovni předplatného a zásadou na úrovni skupiny prostředků má přednost zásada na úrovni skupiny prostředků.
>
>

Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení v závislosti na nastavení v zásadách zabezpečení ovládací prvky. Doporučení vás provede procesem konfigurace potřebných bezpečnostní kontroly.

Aktuální zásady doporučení v Security Center zaměřit se na aktualizace systému, konfigurace operačního systému, skupiny zabezpečení na podsítě a virtuální počítače (VM), auditování služby SQL Database, SQL Database TDE, sítě a brány firewall webových aplikací. Nejaktuálnější pokrytí doporučení služby Security Center, najdete v části [Správa doporučení zabezpečení v Security Center](security-center-recommendations.md).

## <a name="scenario"></a>Scénář
Tento scénář popisuje, jak používat Security Center Chcete-li snížit riziko významný bezpečnostní incident podle doporučení služby Security Center monitorování a provádění akcí. Tento scénář používá fiktivní společnosti, Contoso a rolí uváděné v Centru zabezpečení [Průvodce plánováním a provozem](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). Role představují jednotlivcům i týmům, které mohou používat Security Center k provádění různých úloh souvisejících se zabezpečením. Role jsou:

![Scénář role][2]

Contoso nedávno provedla migraci některých místních prostředků do Azure. Contoso chce implementovat a udržovat ochrany, které snižují jejich zranitelnost vůči útoku zabezpečení svých prostředků v cloudu.

## <a name="recommended-solution"></a>Doporučené řešení
Řešení je začít používat Security Center k zabránění a zjišťování ohrožení zabezpečení. Contoso má přístup ke službě Security Center prostřednictvím svého předplatného Azure. [Úroveň Free](security-center-pricing.md) ze Security Center je automaticky povolená na všech předplatných Azure a je povolené shromažďování dat pro všechny virtuální počítače v rámci svého předplatného.

David, zabezpečení IT společnosti Contoso, konfiguruje **zásady zabezpečení** pomocí služby Security Center. Security Center analyzuje stav zabezpečení prostředků Azure společnosti Contoso. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří **doporučení** podle nastavení v zásadách zabezpečení ovládací prvky.

Jan, vlastníkem úlohy v cloudu, je zodpovědný za implementaci a správu bezpečnostních mechanismů v souladu se zásadami zabezpečení společnosti Contoso. Jan může monitorovat doporučení vytvořených službou Security Center k použití ochrany. Doporučení Jan provede procesem konfigurace zabezpečení potřebných kontrol.

Aby Jan k implementaci a udržování ochrany a odstranění ohrožení zabezpečení potřebné informace pro:

- Doporučení pro monitorování zabezpečení poskytne Security Center
- Vyhodnocení doporučení týkající se zabezpečení a rozhodnout, jestli má použít nebo zavřít
- Použití doporučení k zabezpečení

Pojďme kroků Janova naleznete v tématu jak používá doporučení služby Security Center k mu provede procesem konfigurace ovládacích prvků k odstranění ohrožení zabezpečení.

## <a name="how-to-implement-this-solution"></a>Postup implementace tohoto řešení
Jan přihlásí k [webu Azure portal](https://azure.microsoft.com/features/azure-portal/) a otevře se konzole Security Center. Jako součást své denní aktivity sledování mohl zkontroluje, pokud jsou doporučení zabezpečení podle následujících kroků:

1. Jan vybere **doporučení** dlaždici otevřete **doporučení**.
   ![Vyberte dlaždici doporučení][3]
2. Jan zkontroluje seznam doporučení. Zjistí, že Security Center poskytuje seznam doporučení v pořadí podle priority od nejvyšší priority k nejnižší prioritu. Rozhodne se řešit doporučení s vysokou prioritou v seznamu. Vybere **nainstalovat službu Endpoint Protection** pod **doporučení**.
3. **Nainstalovat službu Endpoint Protection** otevře se zobrazení seznamu virtuálních počítačů bez povolený antimalware v programu. Jan zkontroluje seznam virtuálních počítačů, vybere všechny virtuální počítače a pak vybere **nainstalovat na 3 virtuální počítače**.
   ![Instalace Endpoint Protection][4]
4. **Vybrat Endpoint Protection** otevře poskytování Jan dvě antimalwarová řešení. Jan vybere **Microsoft Antimalware** řešení.
5. Zobrazí se další informace o řešení proti malwaru. Jan vybere **vytvořit**.
   ![Antimalware od Microsoftu][5]
6. Jan zadá požadované konfigurační nastavení v části **nainstalovat** a vybere **OK**.

[Microsoft Antimalware](../security/azure-security-antimalware.md) je teď aktivní na vybrané virtuální počítače.

Jan pokračuje procházení s vysokou prioritou a doporučení pro Střední priorita, rozhodování v implementaci. Jan odkazuje [Správa doporučení zabezpečení](security-center-recommendations.md) článek k pochopení doporučení a každý z nich, co dělá pokud mu vztahuje.

Jan se učí, který [Microsoft Security Response Center (MSRC)](../security/azure-security-response-center.md) provádí monitorování zabezpečení vyberte síť Azure a infrastrukturou a přijímá threat intelligence a zneužití stížností od třetích stran. Pokud Jan poskytuje podrobností o kontaktu zabezpečení pro předplatné Azure společnosti Contoso, Microsoft kontakty společnosti Contoso, pokud zjistí, MSRC zákaznických dat společnosti Contoso přistupovala nezákonně nebo neoprávněně stran. Pojďme postupujte podle Jan mu se vztahuje **poskytnutí podrobností kontaktů zabezpečení** doporučení (doporučení se závažností Střední seznam doporučení výše).

1. Jan vybere **poskytnutí podrobností kontaktů zabezpečení** pod **doporučení**, které se otevře **poskytnutí podrobností kontaktů zabezpečení**.
2. Jan vybere předplatné Azure, které poskytují kontaktní informace o. Sekundy **poskytnutí podrobností kontaktů zabezpečení** se otevře okno.
   ![Podrobností o kontaktu zabezpečení][6]
3. V části **poskytnutí podrobností kontaktů zabezpečení**, Jan zadá:

  - Kontaktní e-mailové adresy zabezpečení oddělených čárkami (není omezený počet e-mailové adresy, které může zadat)
  - Obraťte se na telefonní číslo jednoho zabezpečení

4. Jan také zapíná možnost **zaslat e-mailem o výstrahách** dostávat e-maily o výstrahách s vysokou závažností.
5. Jan vybere **OK** použít informace o kontaktu zabezpečení do předplatného společnosti Contoso.

Nakonec Jan zkontroluje doporučení s nízkou prioritou **ohrožení zabezpečení operačního systému opravit** a určuje, že toto doporučení se nedá použít. Chce zavřít doporučení. Jan vybere na tři tečky, které se zobrazí na pravé straně a pak vybere **Zavřít**.
   ![Zavřít doporučení][7]

## <a name="conclusion"></a>Závěr
Doporučení ve službě Security Center pro monitorování může pomoct eliminovat ohrožení zabezpečení, než dojde k útoku. Je-li zabránit incidentu zabezpečení, implementaci a správu bezpečnostních mechanismů pomocí zásad zabezpečení ve službě Security Center.

## <a name="next-steps"></a>Další postup
Tento scénář vám ukázali, jak používat zásady zabezpečení a doporučení ve službě Security Center můžete zmírnit útok na zabezpečení. Zobrazit [scénáři reakce na incidenty](security-center-incident-response.md) se naučíte být při reakci na incidenty připravený plán předtím, než k útoku dojde.

Další informace o službě Security Center najdete v tématu:

* [Sledování stavu zabezpečení](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reagovat na výstrahy zabezpečení.
* [Sledování a zpracování událostí zabezpečení](security-center-events-dashboard.md) – zjistěte, jak monitorovat a shromažďovat události zabezpečení procesu v čase.
* [Sledování partnerských řešení](security-center-partner-solutions.md) – zjistěte, jak sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější zprávy zabezpečení Azure a informace.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png
