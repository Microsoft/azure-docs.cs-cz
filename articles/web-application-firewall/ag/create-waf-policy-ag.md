---
title: Vytvoření zásad brány webových aplikací (WAF) pro aplikační bránu
description: Přečtěte si, jak vytvořit zásady brány firewall webových aplikací pro aplikační bránu.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 3e8cd2f1e594cd6a60296b2df135f275641df313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086979"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Vytvoření zásad brány firewall webových aplikací pro bránu aplikací

Sladění zásad WAF s naslouchacími procesy umožňuje ochranu více webů za jedním wafem různými zásadami. Pokud je například za vaším wafem pět webů, můžete mít pět samostatných zásad WAF (jeden pro každý naslouchací proces) pro přizpůsobení vyloučení, vlastních pravidel a spravovaných pravidel pro jeden web bez provedení dalších čtyř. Pokud chcete, aby se jedna zásada vztahovala na všechny weby, můžete zásadu přidružit k bráně aplikace, nikoli k jednotlivým naslouchavkým posluchačům, aby se vztahovala globálně. Zásady lze také použít pro pravidlo směrování založené na cestě. 

Můžete vytvořit tolik zásad, kolik chcete. Jakmile vytvoříte zásadu, musí být přidružena k bráně aplikace, aby se projevila, ale může být přidružena k libovolné kombinaci aplikačních bran a naslouchacích procesů. 

Pokud vaše aplikační brána má zásady použít a potom použít jinou zásadu naslouchací proces na této aplikační brány, naslouchací proces zásady se projeví, ale pouze pro posluchače, které jsou přiřazeny. Zásady Brány aplikace stále platí pro všechny ostatní naslouchací procesy, které nemají přiřazeny konkrétní zásady. 

   > [!NOTE]
   > Zásady WAF pro vlastní web a uri jsou ve verzi Public Preview. To znamená, že tato funkce podléhá doplňkovým podmínkám použití společnosti Microsoft. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Všechna nová nastavení WAF brány firewall webových aplikací (vlastní pravidla, spravované konfigurace rulse, vyloučení atd.) žijí v rámci zásad WAF. Pokud máte existující WAF, tato nastavení mohou stále existovat v konfiguraci WAF. Postup přechodu na nové zásady WAF naleznete v [tématu Migrace konfigurace WAF na zásady WAF](#migrate) dále v tomto článku. 

## <a name="create-a-policy"></a>Vytvoření zásad

Nejprve vytvořte základní zásadu WAF se spravovanou výchozí sadou pravidel (DRS) pomocí portálu Azure.

1. V levé horní části portálu vyberte **Vytvořit prostředek**. Vyhledejte **waf**, vyberte **bránu firewall webové aplikace**a vyberte příkaz **Vytvořit**.
2. Na stránce **Zásady Vytvoření waf,** **základní** kartu, zadejte nebo vyberte následující informace, přijmout výchozí hodnoty pro zbývající nastavení a pak vyberte **Zkontrolovat + vytvořit**:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Politika pro     |Regionální WAF (aplikační brána)|
   |Předplatné     |Vyberte název předplatného.|
   |Skupina prostředků     |Vyberte skupinu prostředků.|
   |Název zásad     |Zadejte jedinečný název zásad WAF.|
3. Na kartě **Přidružení** zadejte jedno z následujících nastavení a pak vyberte **Přidat**:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Přidružit aplikační bránu     |Vyberte název profilu aplikační brány.|
   |Přidružit posluchače     |Vyberte název posluchače aplikační brány a pak vyberte **Přidat**.|

   > [!NOTE]
   > Pokud přiřadíte zásadu vaší aplikační bráně (nebo naslouchací proces), který již má zásady na místě, původní zásady je přepsána a nahrazena novou zásadou.
4. Vyberte **Zkontrolovat + vytvořit**, pak vyberte **Vytvořit**.

   ![Základy zásad WAF](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Konfigurace pravidel WAF (nepovinné)

Když vytvoříte zásadu WAF, ve výchozím nastavení je v režimu *zjišťování.* V režimu detekce WAF neblokuje žádné požadavky. Místo toho jsou do protokolů WAF zaznamenána odpovídající pravidla WAF. Chcete-li zobrazit waf v akci, můžete změnit nastavení režimu na *Prevence*. V režimu prevence jsou pravidla párování definovaná ve vybrané sadě pravidel crs blokována a/nebo zaznamenána v protokolech WAF.

## <a name="managed-rules"></a>Spravovaná pravidla

Pravidla OWASP spravovaná azure jsou ve výchozím nastavení povolená. Chcete-li zakázat jednotlivé pravidlo v rámci skupiny pravidel, rozbalte pravidla v rámci této skupiny pravidel, zaškrtněte políčko před číslem pravidla a na kartě výše vyberte **Zakázat.**

[![Spravovaná pravidla](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Vlastní pravidla

Pokud chcete vytvořit vlastní pravidlo, vyberte **Přidat vlastní pravidlo** na kartě Vlastní **pravidla.** Otevře se stránka konfigurace vlastního pravidla. Následující snímek obrazovky ukazuje příklad vlastního pravidla nakonfigurovaného tak, aby blokoval požadavek, pokud řetězec dotazu obsahuje *blok textu*.

[![Upravit vlastní](../media/create-waf-policy-ag/edit-custom-rule.png) pravidlo](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>Migrace konfigurace WAF do zásad WAF

Pokud máte existující WAF, možná jste si všimli některých změn na portálu. Nejprve je třeba zjistit, jaký druh zásad jste povolili na waf. Existují tři potenciální stavy:

- Žádná zásada WAF
- Pouze vlastní pravidla zásady
- Zásady WAF

Můžete zjistit, ve kterém stavu se váš WAF nachází, a to tak, že se na něj podíváte na portálu. Pokud jsou nastavení WAF viditelná a lze ji změnit ze zobrazení Aplikační brána, je váš WAF ve stavu 1.

[![Konfigurace](../media/create-waf-policy-ag/waf-configure.png) WAF](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Pokud vyberete **bránu firewall webové aplikace** a zobrazí se přidružená zásada, waf je ve stavu 2 nebo stavu 3. Po přechodu na zásadu, pokud se zobrazí **pouze** vlastní pravidla a přidružené aplikační brány, pak je to pouze vlastní pravidla zásady.

![Vlastní pravidla WAF](../media/create-waf-policy-ag/waf-custom-rules.png)

Pokud se také zobrazuje nastavení zásad a spravovaná pravidla, pak se jedná o úplné zásady brány firewall webových aplikací. 

![Nastavení zásad WAF](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migrace na zásady WAF

Pokud máte vlastní pravidla pouze waf zásady, pak můžete chtít přejít na nové zásady WAF. Do budoucna bude zásada brány firewall podporovat nastavení zásad WAF, spravované sady pravidel, vyloučení a zakázané skupiny pravidel. V podstatě všechny konfigurace WAF, které byly dříve provedeny uvnitř aplikační brány, jsou nyní prováděny prostřednictvím zásad WAF. 

Úpravy vlastní pravidlo pouze WAF zásady jsou zakázány. Chcete-li upravit všechna nastavení WAF, jako je zakázání pravidel, přidání vyloučení atd., musíte migrovat na nový prostředek zásad brány firewall nejvyšší úrovně.

Chcete-li tak učinit, vytvořte *zásady brány firewall webové aplikace* a přidružte ji k bráně aplikace a posluchačům, které si zvolíte. Tato nová zásada **musí** být přesně stejná jako aktuální konfigurace WAF, což znamená, že každé vlastní pravidlo, vyloučení, zakázané pravidlo atd. Jakmile máte zásady přidružené k bráně aplikace, můžete pokračovat v provádění změn pravidel a nastavení WAF. Můžete to udělat také s Azure PowerShell. Další informace naleznete [v tématu Přidružení zásad WAF k existující bráně aplikace](associate-waf-policy-existing-gateway.md).

Volitelně můžete použít skript migrace k migraci do zásadwaf. Další informace najdete v tématu [Migrace zásad brány firewall webových aplikací pomocí Azure PowerShellu](migrate-policy.md).

## <a name="next-steps"></a>Další kroky

Další informace o [skupinách a pravidlech](application-gateway-crs-rulegroups-rules.md)serveru FIREWALL brány firewall webových aplikací .
