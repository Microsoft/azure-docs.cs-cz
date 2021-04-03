---
title: Implementace technologie QoS (Quality of Service) pro virtuální počítače s Windows (Preview)
titleSuffix: Azure
description: Jak nastavit technologii QoS (Preview) pro virtuální počítač s Windows
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94639253"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>Implementace technologie QoS (Quality of Service) pro virtuální počítače s Windows (Preview)

> [!IMPORTANT]
> Podpora zásad technologie QoS (Quality of Service) pro virtuální počítače s Windows je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[RDP ShortPath](./shortpath.md) poskytuje přímý přenos založený na UDP mezi klientem vzdálené plochy a hostitelem relace. RDP ShortPath umožňuje konfiguraci zásad technologie QoS (Quality of Service) pro data RDP.
Technologie QoS ve virtuálním počítači s Windows umožňuje provoz protokolu RDP v reálném čase, který je citlivý na zpoždění sítě, na konci přenosu dat, která jsou míň citlivá. Příkladem takového méně citlivého provozu je stažení nové aplikace, kde další sekunda ke stažení není velká koupě. Technologie QoS používá objekty Windows Zásady skupiny k identifikaci a označení všech paketů v datových proudech v reálném čase a umožňuje vaší síti poskytovat přenosy protokolu RDP vyhrazenou část šířky pásma.

Pokud podporujete velkou skupinu uživatelů, kteří mají některý z problémů popsaných v tomto článku, budete pravděpodobně muset implementovat technologii QoS. Malé firmy s malým počtem uživatelů nemusí technologii QoS potřebovat, ale měla by být užitečná i tam.

Bez určité formy technologie QoS se může zobrazit následující problémy:

* Kolísání – pakety RDP přicházejí v různých tarifech, což může vést k vizuálním a audio histogramu
* Ztráta paketů – vyřazené pakety, což vede k opětovnému přenosu, který vyžaduje další čas
* Opožděná doba odezvy (RTT) – pakety protokolu RDP trvá dlouhou dobu, než se dostanou na jejich cíle, což vede k znatelnému zpoždění mezi vstupem a reakcí ze vzdálené aplikace.

Nejjednodušším způsobem, jak tyto problémy vyřešit, je zvýšit velikost datových připojení interně i na Internet. Vzhledem k tomu, že je to často nákladově úsporné, technologie QoS nabízí způsob, jak spravovat prostředky, které máte, místo abyste přidávali šířku pásma efektivněji. Abyste vyřešili problémy s kvalitou, doporučujeme, abyste nejdřív používali technologii QoS a pak mohli přidat šířku pásma pouze v případě potřeby.

Aby technologie QoS byla účinná, musíte v celé organizaci použít konzistentní nastavení QoS. Jakákoli část cesty, která nedokáže podporovat vaše priority QoS, může snížit kvalitu relace RDP.

## <a name="introduction-to-qos-queues"></a>Seznámení s frontami technologie QoS

Aby bylo možné zajistit technologii QoS, musí mít síťová zařízení možnost klasifikace provozu a musí být schopna rozlišovat protokol RDP od ostatních síťových přenosů.

Když síťový provoz vstoupí do směrovače, provoz se umístí do fronty. Pokud Zásada QoS není nakonfigurovaná, existuje jenom jedna fronta a všechna data se považují za první, nejdřív se stejnou prioritou. To znamená, že provoz protokolu RDP se může zablokovat za provozu, kdy pár dalších milisekund nepředstavuje problém.

Při implementaci technologie QoS definujete více front pomocí jedné z několika funkcí pro správu zahlcení, jako jsou například služby přidělování priorit společnosti Cisco a vážené přiřazení [na základě tříd (CBWFQ)](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) a funkce zabraňující zahlcení, jako je například [vážené náhodné zjišťování (WRED)](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html).

Jednoduchá analogie je, že technologie QoS ve vaší datové síti vytvoří virtuální "carpoolelné dráhy". Takže některé typy dat nikdy nebo zřídka nastanou zpoždění. Jakmile vytvoříte tyto koridory, můžete upravit jejich relativní velikost a mnohem efektivněji spravovat šířku pásma připojení, kterou máte, a přitom stále dodávat firemní prostředí pro uživatele vaší organizace.

## <a name="qos-implementation-checklist"></a>Kontrolní seznam implementace technologie QoS

Na nejvyšší úrovni proveďte následující kroky k implementaci technologie QoS:

1. [Ujistěte se, že je vaše síť připravená.](#make-sure-your-network-is-ready)
2. Ujistěte se [, že je povolený protokol RDP ShortPath](./shortpath.md) – pro přenos zpětného připojení se nepodporují zásady QoS.
3. [Implementace vložení značek DSCP](#insert-dscp-markers) na hostitele relací

Při přípravě na implementaci technologie QoS mějte na paměti následující pokyny:

* Nejkratší cesta k hostiteli relace je nejlepší
* Nejsou doporučovány žádné překážky mezi, například proxy servery nebo zařízení kontroly paketů.

## <a name="make-sure-your-network-is-ready"></a>Ujistěte se, že je vaše síť připravená.

Pokud zvažujete implementaci QoS, měli byste už stanovit požadavky na šířku pásma a jiné požadavky na [síť](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context).
  
Zahlcení provozu napříč sítí významně ovlivní kvalitu multimédií. Nedostatek šířky pásma vede ke snížení výkonu a špatnému uživatelskému prostředí. Při zvětšování a používání virtuálních počítačů s Windows je možné pomocí [Log Analytics](./diagnostics-log-analytics.md) identifikovat problémy a pak provádět úpravy pomocí přidaných prostředků QoS a selektivní šířky pásma.

### <a name="vpn-considerations"></a>Doporučení k síti VPN

Technologie QoS funguje jenom podle očekávání, pokud je implementovaná na všech propojeních mezi klienty a hostiteli relací. Pokud použijete technologii QoS v interní síti a uživatel se přihlásí ze vzdáleného umístění, můžete určit prioritu pouze v rámci vaší interní, spravované sítě. I když Vzdálená umístění můžou získat spravované připojení implementací virtuální privátní sítě (VPN), síť VPN sám přičítá nároky na pakety a vytváří zpoždění v provozu v reálném čase.

V globální organizaci se spravovanými odkazy, které přesahují kontinenty, důrazně doporučujeme technologii QoS, protože šířka pásma pro tyto odkazy je v porovnání s místní sítí omezená.

## <a name="insert-dscp-markers"></a>Vložit značky DSCP

Technologie QoS se dá implementovat pomocí objektu Zásady skupiny (GPO), který bude směrovat hostitele relace tak, aby se do hlaviček paketů IP vložila značka DSCP, která ji identifikuje jako konkrétní typ provozu. Směrovače a další síťová zařízení je možné nakonfigurovat pro rozpoznání těchto označení a přenos dat do samostatné fronty s vyšší prioritou.

Označení DSCP se dá porovnávat s razítky, které označují poštovním pracovníkům, jak urgentní doručení je a jak nejlépe je seřadit pro urychlené doručování. Po nakonfigurování sítě tak, aby poskytovala přednost datovým proudům protokolu RDP, by se ztratily pakety a pozdě pakety významně snížily.

Jakmile všechna síťová zařízení používají stejné klasifikace, označení a priority, je možné snížit nebo odstranit prodlevy, ztracené pakety a kolísání. V perspektivě protokolu RDP je základní krok konfigurace klasifikace a označení paketů. Aby se ale komplexní technologie QoS úspěšně provedla, musíte pečlivě zarovnat konfiguraci RDP s podkladovou konfigurací sítě.
Hodnota DSCP oznamuje odpovídající nakonfigurovanou síť, jakou prioritu má poskytovat paket nebo datový proud.

Doporučujeme použít hodnotu DSCP 46, která je mapována na **urychlené předávání (EF)** třídy DSCP.

### <a name="implement-qos-on-session-host-using-group-policy"></a>Implementace technologie QoS na hostiteli relace pomocí Zásady skupiny

V rámci Zásady skupiny můžete použít technologii QoS (Quality of Service) založenou na zásadách pro nastavení předdefinované hodnoty DSCP.

Chcete-li vytvořit zásadu QoS pro hostitele relace připojené k doméně, nejprve se přihlaste k počítači, na kterém je nainstalovaná Zásady skupiny Správa. Otevřete správu Zásady skupiny (vyberte Start, přejděte na nástroje pro správu a pak vyberte Zásady skupiny Správa) a pak proveďte následující kroky:

1. V části Správa Zásady skupiny Najděte kontejner, ve kterém se má vytvořit nová zásada. Pokud je například každá vaše relace hostitelem počítačů umístěná v organizační jednotce s názvem **"hostitelé relace"**, je nutné vytvořit novou zásadu v relaci, která je hostitelem organizační jednotky.

2. Klikněte pravým tlačítkem na příslušný kontejner a pak vyberte **vytvořit objekt zásad skupiny v této doméně a propojit ho sem**.

3. V dialogovém okně **Nový objekt zásad skupiny** zadejte do pole **název** název nového objektu Zásady skupiny a pak vyberte **OK**.

4. Klikněte pravým tlačítkem na nově vytvořenou zásadu a pak vyberte **Upravit**.

5. V Editor pro správu zásad skupiny rozbalte položku **Konfigurace počítače**, rozbalte **nastavení systému Windows**, klikněte pravým tlačítkem na možnost **QoS na základě zásad** a pak vyberte **vytvořit novou zásadu**.

6. V dialogovém okně **technologie QoS založené na zásadách** zadejte na úvodní stránce název nové zásady do pole **název** . Vyberte **zadat hodnotu DSCP** a nastavte hodnotu na **46**. Nechejte možnost zadat nevybranou **míru odchozího omezení** a pak vyberte **Další**.

7. Na další stránce vyberte **jenom aplikace s tímto názvem spustitelného souboru** , zadejte název **svchost.exe** a pak vyberte **Další**. Toto nastavení instruuje zásady tak, aby odpovídaly prioritám odpovídajícího provozu ze služby Vzdálená plocha.

8. Na třetí stránce se ujistěte, že je vybraná **libovolná zdrojová IP adresa** i **jakákoli cílová IP adresa** , a pak vyberte **Další**. Tato dvě nastavení zajišťují, že se pakety budou spravovat bez ohledu na to, který počítač (IP adresa) pakety odeslal a které počítače (IP adresa) budou přijímat pakety.

9. Na stránce 4 vyberte možnost **UDP** v rozevíracím seznamu **Vyberte protokol, který se vztahuje na zásadu QoS** .

10. V části **Zadejte číslo zdrojového portu** vyberte možnost **z tohoto zdrojového portu nebo rozsahu**. Do doprovodného textového pole zadejte **3390**. Vyberte **Dokončit**.

Nové zásady, které jste vytvořili, se projeví až po aktualizaci Zásady skupiny na hostitelských počítačích relací. I když se Zásady skupiny pravidelně aktualizuje sami, můžete vynutit okamžitou aktualizaci pomocí následujících kroků:

1. Na každém hostiteli relace, pro který chcete aktualizovat Zásady skupiny otevřete příkazový řádek jako správce (*Spustit jako správce*).

1. Do příkazového řádku zadejte

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>Implementace technologie QoS na hostiteli relace pomocí prostředí PowerShell

QoS pro ShortPath RDP můžete nastavit pomocí rutiny PowerShellu níže:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>Související články

* [Zásada technologie QoS (Quality of Service)](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>Další kroky

* Další informace o požadavcích na šířku pásma pro virtuální počítač s Windows najdete v článku [Principy požadavků na šířku pásma protokol RDP (Remote Desktop Protocol) (RDP) pro virtuální počítač s Windows](rdp-bandwidth.md).
* Další informace o připojení k síti virtuálních počítačů s Windows najdete v tématu [Principy připojení k síti virtuálních počítačů s Windows](network-connectivity.md).
