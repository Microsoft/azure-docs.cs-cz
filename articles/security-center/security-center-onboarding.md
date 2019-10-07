---
title: Připojování k Azure Security Center Standard pro rozšířené zabezpečení | Microsoft Docs
description: " Naučte se, jak se připojit k Azure Security Center Standard pro rozšířené zabezpečení. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: memildin
ms.openlocfilehash: 01cc625767cbf437031f503cf4b0d4dc59b62e08
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996296"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Připojování k Azure Security Center Standard pro rozšířené zabezpečení
Upgradujte na Security Center Standard, abyste mohli využívat rozšířenou správu zabezpečení a ochranu před hrozbami pro vaše hybridní cloudové úlohy.  Můžete vyzkoušet Standard zdarma. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/) Security Center.

Security Center Standard zahrnuje:

- **Hybridní zabezpečení** – Získejte jednotný přehled o zabezpečení napříč všemi vašimi místními i Cloud úlohami. Použijte zásady zabezpečení a průběžně vyhodnoťte zabezpečení vašich hybridních cloudových úloh, abyste zajistili dodržování standardů zabezpečení. Shromažďování, vyhledávání a analýza dat zabezpečení z nejrůznějších zdrojů, včetně bran firewall a dalších partnerských řešení.
- **Rozšířená detekce hrozeb** – použití pokročilých analýz a Microsoft Intelligent Security Graph k získání hraničních zařízení, která vyvíjejí počítačové útoky.  Využijte integrované analýzy chování a strojové učení k identifikaci útoků a využívání funkce Zero Day. Monitorujte sítě, počítače a cloudové služby pro příchozí útoky a činnost po porušení zabezpečení. Zjednodušte šetření pomocí interaktivních nástrojů a kontextové analýzy hrozeb.
- **Řízení přístupu a aplikací** – zablokuje malware a další nežádoucí aplikace pomocí doporučení na seznam povolených, která jsou přizpůsobená vašim konkrétním úlohám a využívají Machine Learning. Snižte úroveň útoku na síť pomocí řízeného přístupu za běhu k portům pro správu virtuálních počítačů Azure, což výrazně snižuje riziko útoků hrubou silou a dalších sítí.

## <a name="detecting-unprotected-resources"></a>Zjišťování nechráněných prostředků     
Security Center automaticky detekuje všechna předplatná Azure nebo pracovní prostory, které nejsou povolené pro Security Center Standard. To zahrnuje předplatná Azure využívající Security Center volno a pracovní prostory, které nemají povolené řešení zabezpečení.

Můžete upgradovat celé předplatné Azure na úroveň Standard, která se dědí ze všech podporovaných prostředků v rámci předplatného. Použití úrovně Standard na pracovní prostor se vztahuje na všechny prostředky, které jsou v pracovním prostoru vydávány.

> [!NOTE]
> Můžete chtít spravovat náklady a omezit množství shromažďovaných dat pro řešení tím, že je omezíte na konkrétní sadu agentů. [Cílení na řešení](../operations-management-suite/operations-management-suite-solution-targeting.md) umožňuje použít obor pro řešení a cílit na podmnožinu počítačů v pracovním prostoru.  Pokud používáte cílení řešení, Security Center zobrazí seznam pracovních prostorů, které nemají řešení.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Upgrade předplatného nebo pracovního prostoru Azure
Upgrade předplatného nebo pracovního prostoru na standard:
1. V hlavní nabídce Security Center vyberte **Začínáme**.
  ![Začínáme](./media/security-center-onboarding/get-started.png)
2. V části **Upgrade**Security Center seznam předplatných a pracovních prostorů, které mají nárok na registraci. 
   - Můžete kliknout na rozbalitelné **použití zkušební verze** , abyste viděli seznam všech předplatných a pracovních prostorů se stavem nároku na zkušební verzi.
   -    Můžete upgradovat předplatná a pracovní prostory, které nejsou vhodné k vyzkoušení.
   -    Můžete vybrat opravňující pracovní prostory a odběry, abyste mohli zahájit zkušební období.
3.  Kliknutím na **zahájit zkušební období** spustíte zkušební verzi na vybraných předplatných.
  předplatné ![Select @ no__t-1


   > [!NOTE]
   > Možnosti bezplatného Security Center se uplatní jenom pro virtuální počítače Azure a jenom pro VMSS. Bezplatné možnosti se nevztahují na počítače mimo Azure. Pokud vyberete možnost standard, budou se standardní možnosti použít u všech virtuálních počítačů Azure, virtuálních počítačů a počítačů mimo Azure, které vytvářejí sestavy do pracovního prostoru. Doporučujeme použít standard, abyste zajistili rozšířené zabezpečení pro prostředky Azure i mimo Azure.
   >
   >

## <a name="onboard-non-azure-computers"></a>Připojování počítačů mimo Azure
Security Center můžou monitorovat stav zabezpečení počítačů mimo Azure, ale je potřeba nejdřív tyto prostředky připojit. Počítače mimo Azure můžete přidat z okna **Začínáme** nebo z okna **COMPUTE** . Provedeme vás oběma metodami.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Přidání nových počítačů mimo Azure z **Začínáme**

1. Vraťte se do části **Začínáme**.   
2. Vyberte kartu **Začínáme** .

   ![Mimo Azure](./media/security-center-onboarding/non-azure.png)

3. V části **Přidat nové počítače mimo Azure**klikněte na **Konfigurovat** . Zobrazí se seznam vašich Log Analyticsch pracovních prostorů. Seznam obsahuje (Pokud je k dispozici) výchozí pracovní prostor, který jste vytvořili Security Center, když bylo povoleno automatické zřizování. Vyberte tento pracovní prostor nebo jiný pracovní prostor, který chcete použít.

   ![Přidat počítač mimo Azure][7]

Pokud máte existující pracovní prostory, jsou uvedené v části **Přidat nové počítače mimo Azure**. Můžete přidat počítače do existujícího pracovního prostoru nebo vytvořit nový pracovní prostor. Pokud chcete vytvořit nový pracovní prostor, vyberte odkaz **Přidat nový pracovní prostor**.

### <a name="add-new-non-azure-computers-from-compute"></a>Přidat nové počítače mimo Azure z **COMPUTE**

**Vytvořit nový pracovní prostor a přidat počítač**

1. V části **Přidat nové počítače mimo Azure**vyberte **Přidat nový pracovní prostor**.

   ![Přidat nový pracovní prostor][4]

2. V části **Security and Audit**vyberte **pracovní prostor OMS** a vytvořte nový pracovní prostor.
   > [!NOTE]
   > Pracovní prostory OMS se teď označují jako Log Analytics pracovní prostory.
3. V části **pracovní prostor OMS**zadejte informace pro svůj pracovní prostor.
4. V části **pracovní prostor OMS**vyberte **OK**.  Po výběru OK získáte odkaz pro stažení agenta pro Windows nebo Linux a klíčů pro ID vašeho pracovního prostoru, který se použije při konfiguraci agenta.
5. V části **Security and Audit**vyberte **OK**.

**Vyberte existující pracovní prostor a přidejte počítač.**

Počítač můžete přidat pomocí pracovního **postupu z registrace, jak je uvedeno**výše. Počítač můžete také přidat pomocí pracovního postupu z **COMPUTE**. V tomto příkladu používáme **COMPUTE**.

1. Vraťte se do hlavní nabídky Security Center a na řídicí panel **Přehled** .

   ![Přehled][5]

2. Vyberte **výpočetní & aplikace**.
3. V části **výpočetní & aplikace**vyberte **přidat počítače**.

   ![Okno COMPUTE][6]

4. V části **Přidat nové počítače mimo Azure**vyberte pracovní prostor, ke kterému se má počítač připojit, a klikněte na **přidat počítače**.

   ![Přidat počítače][7]

   Okno **přímý agent** poskytuje odkaz pro stažení agenta systému Windows nebo Linux a ID a klíče pracovního prostoru pro použití při konfiguraci agenta.   

## <a name="next-steps"></a>Další kroky
V tomto článku jste se naučili, jak začlenit prostředky Azure a jiné než Azure, aby bylo možné využívat pokročilé zabezpečení Security Center.  Další informace o prostředcích připojení najdete v tématu.

- [Povolit shromažďování dat](security-center-enable-data-collection.md)
- [Sestava analýzy hrozeb](security-center-threat-report.md)
- [Přístup k virtuálnímu počítači za běhu](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
