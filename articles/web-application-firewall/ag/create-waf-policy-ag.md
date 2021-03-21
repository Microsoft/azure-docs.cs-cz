---
title: Vytvoření zásad firewallu webových aplikací (WAF) pro Application Gateway
description: Naučte se vytvářet zásady firewallu webových aplikací pro Application Gateway.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 26078c3757e42c3e290a5f4122461b287582fb80
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96518817"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Vytvoření zásad firewallu webových aplikací pro Application Gateway

Přidružení zásady WAF k naslouchacím procesům umožňuje, aby bylo více webů za jedním WAF chráněno pomocí různých zásad. Pokud máte například pět webů za vaší WAF, můžete mít pět samostatných zásad WAF (jeden pro každý naslouchací proces) a přizpůsobit vyloučení, vlastní pravidla a spravované RuleSets pro jednu lokalitu, aniž by to mělo vliv na ostatní čtyři. Pokud chcete, aby se jedna zásada provedla pro všechny lokality, můžete zásadu přidružit k Application Gateway místo jednotlivých posluchačů, aby se mohla použít globálně. Zásady je možné použít také pro pravidlo směrování na základě cesty. 

Můžete vytvořit tolik zásad, kolik chcete. Až zásadu vytvoříte, musí být přidružená k Application Gateway, aby se projevila, ale může být přidružená k jakékoli kombinaci aplikačních bran a posluchačů. 

Pokud je u vašeho Application Gateway použita zásada a potom použijete pro naslouchací proces na tomto Application Gateway jinou zásadu, uplatní se zásada naslouchacího procesu, ale pouze pro naslouchací proces (y), ke kterým jsou přiřazené. Zásady Application Gateway se stále vztahují na všechny ostatní naslouchací procesy, které nemají přiřazené konkrétní zásady. 

   > [!NOTE]
   > Jakmile je zásada brány firewall přidružena k WAF, musí být vždy k této WAF přidružena zásada. Tuto zásadu můžete přepsat, ale zcela se nepodporují zrušením přidružení zásady z WAF. 

Všechna nová nastavení WAF firewallu webových aplikací (vlastní pravidla, spravované konfigurace rulset, vyloučení atd.) se nachází v rámci zásad WAF v reálném čase. Pokud máte existující WAF, tato nastavení můžou pořád existovat v konfiguraci WAF. Postup, jak přejít na nové zásady WAF, najdete v části [migrace konfigurace WAF do zásad WAF](#migrate) dále v tomto článku. 

## <a name="create-a-policy"></a>Vytvoření zásad

Nejdřív vytvořte základní zásadu WAF se spravovanými výchozí sadou pravidel (DRS) pomocí Azure Portal.

1. V levé horní části portálu vyberte **vytvořit prostředek**. Vyhledejte **WAF**, vyberte **Firewall webových aplikací** a pak vyberte **vytvořit**.
2. Na stránce **vytvořit zásadu WAF** na kartě **základy** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Zásady pro     |Oblastní WAF (Application Gateway)|
   |Předplatné     |Vyberte název vašeho předplatného.|
   |Skupina prostředků     |Vyberte skupinu prostředků.|
   |Název zásad     |Zadejte jedinečný název pro zásady WAF.|
3. Na kartě **přidružení** zadejte jedno z následujících nastavení a pak vyberte **Přidat**:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Přidružit Application Gateway     |Vyberte název profilu Application Gateway.|
   |Přidružit naslouchací procesy     |Vyberte název naslouchacího procesu Application Gateway a pak vyberte **Přidat**.|

   > [!NOTE]
   > Pokud přiřadíte zásadu k vašemu Application Gateway (nebo naslouchacího procesu), který už má zásady nastavené, přepíše se původní zásada a nahradí se novými zásadami.
4. Vyberte **Zkontrolovat a vytvořit** a pak **Vytvořit**.

   ![Základy zásad WAF](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Konfigurace pravidel WAF (volitelné)

Když vytvoříte zásadu WAF, ve výchozím nastavení je v režimu *detekce* . V režimu detekce WAF neblokuje žádné požadavky. Místo toho se v protokolech WAF protokolují vyhovující pravidla WAF. Pokud chcete zobrazit WAF v akci, můžete změnit nastavení režimu na *prevence*. V režimu prevence jsou pravidla definovaná v RuleSet, která jste vybrali, blokovaná nebo přihlášená v protokolech WAF.

## <a name="managed-rules"></a>Spravovaná pravidla

Pravidla OWASP spravovaná v Azure jsou ve výchozím nastavení povolená. Pokud chcete v rámci skupiny pravidel zakázat jednotlivá pravidla, rozbalte pravidla v této skupině pravidel, zaškrtněte políčko před číslem pravidla a na kartě výše vyberte **Zakázat** .

[![Spravovaná pravidla ](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Vlastní pravidla

Pokud chcete vytvořit vlastní pravidlo, vyberte na kartě **vlastní pravidla** možnost **Přidat vlastní pravidlo** . Otevře se stránka Konfigurace vlastního pravidla. Následující snímek obrazovky ukazuje příklad vlastního pravidla, které je nakonfigurováno pro blokování požadavku, pokud řetězec dotazu obsahuje text *blockme*.

[![Upravit vlastní pravidlo ](../media/create-waf-policy-ag/edit-custom-rule.png)](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>Migrace konfigurace WAF do zásad WAF

Pokud máte existující WAF, možná jste si všimli nějaké změny na portálu. Nejdřív je potřeba určit, jaký typ zásady jste povolili na WAF. Existují tři možné stavy:

- Žádné zásady WAF
- Zásada jenom pro vlastní pravidla
- Zásady WAF

Můžete určit, ve kterém stavu WAF je, na portálu. Pokud jsou nastavení WAF viditelná a je možné je změnit v zobrazení Application Gateway, vaše WAF je ve stavu 1.

[![Konfigurace ](../media/create-waf-policy-ag/waf-configure.png) WAF](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Pokud vyberete možnost **Firewall webových aplikací** a zobrazí se vám související zásady, WAF je ve stavu 2 nebo 3. Když přejdete na zásadu, pokud se zobrazí **jenom** vlastní pravidla a přidružené aplikační brány, bude se jednat o vlastní pravidla jenom v zásadě.

![Vlastní pravidla WAF](../media/create-waf-policy-ag/waf-custom-rules.png)

Pokud se zobrazuje taky nastavení zásad a spravovaná pravidla, je to úplná zásada firewallu webových aplikací. 

![Nastavení zásad WAF](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migrace na zásady WAF

Pokud máte vlastní pravidla jenom WAF zásady, můžete chtít přejít na nové zásady WAF. Pokud budete pokračovat, zásady brány firewall budou podporovat nastavení zásad WAF, spravované RuleSets, vyloučení a zakázané skupiny pravidel. Všechny konfigurace WAF, které se dřív prováděly v rámci Application Gateway, se teď provádějí prostřednictvím zásad WAF. 

Úpravy pouze zásad WAF jsou zakázané jenom pro vlastní pravidlo. Pokud chcete upravit nastavení WAF, jako je zakázání pravidel, přidání vyloučení atd., musíte migrovat na nový prostředek zásad brány firewall na nejvyšší úrovni.

Provedete to tak, že vytvoříte *zásadu brány firewall webových aplikací* a přidružíte ji k vašim Application Gatewayům a naslouchací proces (y). Tato nová zásada musí být přesně stejná jako aktuální konfigurace WAF, což znamená, že každé vlastní pravidlo, vyloučení, zakázané pravidlo atd. musí být zkopírováno do nové zásady, kterou vytváříte. Jakmile máte k dispozici zásadu přidruženou k vašemu Application Gateway, můžete pokračovat v provádění změn pravidel a nastavení WAF. Můžete to provést také pomocí Azure PowerShell. Další informace najdete v tématu [přidružení zásady WAF k existujícímu Application Gateway](associate-waf-policy-existing-gateway.md).

Volitelně můžete k migraci na zásadu WAF použít migrační skript. Další informace najdete v tématu [migrace zásad firewallu webových aplikací pomocí Azure PowerShell](migrate-policy.md).

## <a name="force-mode"></a>Režim vynucení

Pokud nechcete kopírovat vše do zásad, které jsou přesně stejné jako aktuální konfigurace, můžete nastavit režim WAF na "vynutit". Spusťte následující kód Azure PowerShell a vaše WAF bude v režimu vynucení. Pak můžete k WAF přidružit jakékoli zásady WAF, i když nemá stejné nastavení jako vaše konfigurace. 

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -Name <your Application Gateway name> -ResourceGroupName <your Resource Group name>
$appgw.ForceFirewallPolicyAssociation = $true
```

Pak ProCeeS kroky k přidružení zásady WAF k aplikační bráně. Další informace najdete v tématu [přidružení zásady WAF k existujícímu Application Gateway.](associate-waf-policy-existing-gateway.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [skupinách pravidel a pravidlech pro Firewall webových aplikací](application-gateway-crs-rulegroups-rules.md).
