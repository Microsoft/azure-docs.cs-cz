---
title: Protokol RDP (Remote Desktop Protocol) požadavky na šířku pásma – virtuální plocha Windows – Azure
titleSuffix: Azure
description: Principy požadavků na šířku pásma RDP pro virtuální počítače s Windows
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 708ffce014f417f3794e59e1f79a3fcf9cba3f23
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639199"
---
# <a name="remote-desktop-protocol-rdp-bandwidth-requirements"></a>Požadavky na šířku pásma protokol RDP (Remote Desktop Protocol) (RDP)

Protokol RDP (Remote Desktop Protocol) (RDP) je složitá technologie, která využívá různé techniky k dokonalému doručování vzdálené grafiky serveru do klientského zařízení. V závislosti na případu použití, dostupnosti výpočetních prostředků a šířce pásma sítě protokol RDP dynamicky upravuje různé parametry, aby bylo zajištěno, že bude doručovat nejlepší uživatelské prostředí.

Protokol RDP (Remote Desktop Protocol) multiplexuje více dynamických virtuálních kanálů (DVCs) do jednoho datového kanálu odesílaného přes jiné síťové přenosy. Pro vzdálenou grafiku, vstup, přesměrování zařízení, tisk a další jsou k dispozici samostatné DVCs. Partneři WVD můžou také implementovat svá rozšíření, která používají rozhraní SOUBĚŽNÁ.
Množství dat odesílaných přes protokol RDP závisí na aktivitě uživatele. Uživatel může například pracovat se základním obsahem textu pro většinu relací a využívat minimální šířku pásma, ale následně vygenerovat výtisk 200 dokumentu na místní tiskárně. Tato tisková úloha bude používat velký objem šířky pásma sítě.

Při použití vzdálené relace významně ovlivňuje dostupnou šířku pásma vaší sítě kvalitu vašeho prostředí. Různé aplikace a rozlišení zobrazení vyžadují různé konfigurace sítě, takže je nutné zajistit, aby konfigurace sítě vyhovovala vašim potřebám.

## <a name="estimating-bandwidth-utilization"></a>Odhad využití šířky pásma

Protokol RDP používá pro různé typy dat různé algoritmy komprese. Následující tabulka vysvětluje odhad přenosů dat:

| Typ dat | Směr | Jak odhadnout |
|---|---|---|
| Vzdálená grafika | Hostitel relace do klienta | [Podrobné pokyny najdete v podrobnostech.](#estimating-bandwidth-used-by-remote-graphics) |
| Prezenčních signálů | Oba směry | přibližně 20 bajtů každých 5 sekund  |
| Vstup | Klient pro hostitele relace | Množství dat je založené na aktivitě uživatele, méně než 100 bajtů pro většinu operací.  |
| Přenosy souborů | Oba směry | Přenosy souborů používají hromadnou kompresi. Pro aproximaci použít kompresi. zip |
| Tisk | Hostitel relace do klienta | Přenos tiskových úloh závisí na ovladači a použití hromadné komprese, použití komprese. zip pro aproximaci. |

V jiných scénářích může být požadavky na šířku pásma změny v závislosti na tom, jak je používáte:

* Hlasové nebo video konference
* Komunikace v reálném čase
* Video streamování 4K

## <a name="estimating-bandwidth-used-by-remote-graphics"></a>Odhad šířky pásma používaného vzdálenou grafikou

Předpovídání šířky pásma používá Vzdálená plocha. Uživatelské aktivity generují většinu provozu vzdálené plochy. Každý uživatel je jedinečný a rozdíly v jejich pracovních vzorech můžou významně měnit používání sítě.

Nejlepším způsobem, jak pochopit požadavky na šířku pásma, je monitorování připojení reálných uživatelů. Monitorování se dá provádět pomocí integrovaných čítačů výkonu nebo síťového vybavení.

V mnoha případech ale můžete odhadnout využití sítě tím, že pochopíte, jak protokol RDP (Remote Desktop Protocol) funguje a analyzují pracovní vzorce uživatelů.

Vzdálený protokol poskytuje grafiku vygenerovanou vzdáleným serverem pro zobrazení na místním monitoru. Konkrétně poskytuje rastrový obrázek plochy, který je zcela složený ze serveru.
Při odesílání rastrového obrázku na ploše se zdá jako jednoduchý úkol při prvním přístupu, vyžaduje značný objem prostředků. Například obrázek plochy 1080p v nekomprimované podobě má velikost přibližně 8Mb. Zobrazení tohoto obrázku na místně připojeném monitoru s malou frekvencí obnovení obrazovky 30 Hz vyžaduje šířku pásma přibližně 237 MB/s.

K omezení množství dat přenesených přes síť používá protokol RDP kombinaci několika technik, mimo jiné.

* Optimalizace snímkových frekvencí
* Klasifikace obsahu obrazovky
* Kodeky specifické pro obsah
* Progresivní kódování obrázku
* Ukládání do mezipaměti na straně klienta

Pro lepší pochopení vzdálené grafiky Vezměte v úvahu následující skutečnosti:

* Širší grafika, větší šířka pásma, jakou bude trvat
  * Text, prvky uživatelského rozhraní okna a oblasti plných barev využívají menší šířku pásma než cokoli jiného.
  * Přirozené obrázky představují nejvýznamnější přispěvatele využití šířky pásma. Ale ukládání do mezipaměti na straně klienta pomáhá s jeho snížením.
* Přenáší se jenom změněné části obrazovky. Pokud na obrazovce nejsou žádné viditelné aktualizace, neodesílají se žádné aktualizace.
* Přehrávání videa a další obsah s vysokými rychlostmi jsou v podstatě snímková prezentace. Protokol RDP dynamicky používá příslušné video kodeky k doručování s blíží se původní sazbou snímků. Ale pořád se jedná o grafiku a je to stále nejvýznamnější Přispěvatel k využití šířky pásma.
* Doba nečinnosti ve vzdálené ploše znamená, že nejsou k dispozici žádné nebo minimální aktualizace obrazovky; To znamená, že použití sítě je v době nečinnosti minimální.
* Při minimalizaci okna klienta vzdálené plochy nejsou z hostitele relace odesílány žádné grafické aktualizace.

Mějte na paměti, že zátěže, která je umístěna v síti, závisí na četnosti výstupních snímků úloh aplikace a na rozlišení obrazovky. Pokud se zvýší kmitočet snímků nebo rozlišení obrazovky, bude potřeba taky vyvolávat požadavek na šířku pásma. Například světlá úloha s vysokým rozlišením vyžaduje více dostupné šířky pásma než lehké úlohy s pravidelným nebo nízkým rozlišením. Různá rozlišení zobrazení vyžadují různé dostupné šířky pásma.

Tabulka níže vysvětluje, jak vyodhaduje data používaná v různých grafických scénářích. Tato čísla se vztahují na jednu konfiguraci monitorování s rozlišením 1080 a s výchozím grafickým režimem a s grafickým režimem H. 264/AVC 444.

| Scénář | Výchozí režim | H. 264/režim AVC 444 | Miniatuře | Popis scénáře |
|:---|---:|---:|---|---|
| Období | 0,3 KB/s | 0,3 KB/s |:::image type="content" source="media/idle.png" alt-text="Snímek obrazovky nečinného připojení":::| Uživatel je pozastavený a není k dispozici žádné aktivní aktualizace obrazovky. |
| Microsoft Word | 100-150 kb/s | 200-300 KB/s |:::image type="content" source="media/word.gif" alt-text="Animace Microsoft Wordu":::| Uživatel aktivně pracuje s Microsoft Wordem, psaním a vkládáním grafiky a přepínáním mezi dokumenty. |
| Microsoft Excel | 150-200 KB/s | 400-500 kB/s |:::image type="content" source="media/excel.gif" alt-text="Animace aplikace Microsoft Excel":::| Uživatel aktivně pracuje s aplikací Microsoft Excel, současně se aktualizuje více buněk se vzorci a grafy. |
| Microsoft PowerPoint | 4 – 4.5 MB/s | 1,6 – 1.8 MB/s |:::image type="content" source="media/powerpoint.gif" alt-text="Animace aplikace Microsoft PowerPoint":::| Uživatel aktivně pracuje s Microsoft PowerPointem a probíhá psaní a vkládání. Uživatel také upraví bohatou grafiku a použije efekty přechodu snímků. |
| Procházení webu | 6 – 6.5 MB/s | 0.9 – 1 MB/s |:::image type="content" source="media/web.gif" alt-text="Animace webového procházení":::| Uživatel aktivně pracuje s grafikně bohatou webovou stránku, která obsahuje několik statických a animovaných imagí. Uživatel posouvá stránky vodorovně i svisle. |
| Galerie imagí | 3.3 až 3.6 MB/s | 0,7 – 0,8 MB/s |:::image type="content" source="media/image-gallery.gif" alt-text="Animace Galerie imagí":::| Uživatel aktivně pracuje s aplikací Galerie imagí. procházení, zvětšování a změna velikosti a otáčení obrázků |
| Přehrávání videa | 8.5 – 9,5 MB/s | 2,5 až 2,8 MB/s |:::image type="content" source="media/video.gif" alt-text="Animace přehrávání videa":::| Uživatel sleduje 30 snímků za sekundu, které spotřebovávají 1/2 obrazovky. |
| Přehrávání videa na celé obrazovce | 7,5 – 8.5 MB/s | 2,5 – 3.1 MB/s |:::image type="content" source="media/fullscreen-video.gif" alt-text="Animace přehrávání celoobrazovkového videa":::| Uživatel sleduje 30 snímků za sekundu, který se maximalizuje na celou obrazovku. |

## <a name="dynamic-bandwidth-allocation"></a>Dynamické přidělování šířky pásma

Protokol RDP (Remote Desktop Protocol) je moderní protokol určený k dynamickému přizpůsobení měnícím se podmínkám sítě.
Místo používání pevných omezení využití šířky pásma používá protokol RDP nepřetržitou detekci sítě, která aktivně monitoruje dostupnou šířku pásma sítě a dobu odezvy paketů. Protokol RDP na základě zjištění dynamicky vybírá možnosti kódování grafiky a přiděluje šířku pásma pro přesměrování zařízení a jiné virtuální kanály.  
Tato technologie umožňuje, aby protokol RDP používal plný kanál sítě, pokud je k dispozici, a rychle se zálohuje, když je síť potřebná pro něco jiného.
Protokol RDP detekuje a upravuje kvalitu obrazu, kmitočet snímků nebo kompresní algoritmy, pokud jiné aplikace požadují síť.

## <a name="limit-network-bandwidth-use-with-throttle-rate"></a>Omezení využití šířky pásma sítě s frekvencí omezení

Ve většině scénářů není nutné omezit využití šířky pásma, protože omezení může mít vliv na činnost koncového uživatele. V omezených sítích ale možná budete chtít omezit využití sítě. Dalším příkladem jsou zapůjčené sítě, které se účtují za využité množství provozu.

V takových případech můžete omezit odchozí síťový provoz protokolu RDP zadáním míry omezení v zásadách QoS.

  >[!NOTE]
  > Ujistěte se [, že je povolený protokol RDP ShortPath](./shortpath.md) – pro přenos zpětného připojení se nepodporují omezení rychlosti omezení.

### <a name="implement-throttle-rate-limiting-on-session-host-using-group-policy"></a>Implementace omezení četnosti omezení na hostiteli relace pomocí Zásady skupiny

V rámci Zásady skupiny můžete použít technologii QoS (Quality of Service) založenou na zásadách pro nastavení předem definované míry omezení.

Chcete-li vytvořit zásadu QoS pro hostitele relace připojené k doméně, nejprve se přihlaste k počítači, na kterém je nainstalovaná Zásady skupiny Správa. Otevřete správu Zásady skupiny (vyberte Start, přejděte na nástroje pro správu a pak vyberte Zásady skupiny Správa) a pak proveďte následující kroky:

1. V části Správa Zásady skupiny Najděte kontejner, ve kterém se má vytvořit nová zásada. Pokud je například všechny relace hostitele počítačů umístěny v organizační jednotce s názvem **hostitelé relace** , je nutné vytvořit novou zásadu v relaci HOSTITELSKOU organizační jednotku.

2. Klikněte pravým tlačítkem na příslušný kontejner a pak vyberte **vytvořit objekt zásad skupiny v této doméně a propojit ho sem**.

3. V dialogovém okně **Nový objekt zásad skupiny** zadejte do pole **název** název nového objektu Zásady skupiny a pak vyberte **OK**.

4. Klikněte pravým tlačítkem na nově vytvořenou zásadu a pak vyberte **Upravit**.

5. V Editor pro správu zásad skupiny rozbalte položku **Konfigurace počítače** , rozbalte **nastavení systému Windows** , klikněte pravým tlačítkem na možnost **QoS na základě zásad** a pak vyberte **vytvořit novou zásadu**.

6. V dialogovém okně **technologie QoS založené na zásadách** zadejte na úvodní stránce název nové zásady do pole **název** . Vyberte **zadat míru odchozího omezení** a nastavte požadovanou hodnotu a pak vyberte **Další**.

7. Na další stránce vyberte **jenom aplikace s tímto názvem spustitelného souboru** , zadejte název **svchost.exe** a pak vyberte **Další**. Toto nastavení instruuje zásady tak, aby odpovídaly prioritám odpovídajícího provozu ze služby Vzdálená plocha.

8. Na třetí stránce se ujistěte, že je vybraná **libovolná zdrojová IP adresa** i **libovolná cílová IP adresa** . Vyberte **Další**. Tato dvě nastavení zajišťují, že se pakety budou spravovat bez ohledu na to, který počítač (IP adresa) pakety odeslal a které počítače (IP adresa) budou přijímat pakety.

9. Na stránce 4 vyberte možnost **UDP** v rozevíracím seznamu **Vyberte protokol, který se vztahuje na zásadu QoS** .

10. V části **Zadejte číslo zdrojového portu** vyberte možnost **z tohoto zdrojového portu nebo rozsahu**. Do doprovodného textového pole zadejte **3390**. Vyberte **Dokončit**.

Nové zásady, které jste vytvořili, se projeví až po aktualizaci Zásady skupiny na hostitelských počítačích relací. I když se Zásady skupiny pravidelně aktualizuje sami, můžete vynutit okamžitou aktualizaci pomocí následujících kroků:

1. Na každém hostiteli relace, pro který chcete aktualizovat Zásady skupiny otevřete příkazový řádek jako správce ( *Spustit jako správce* ).

2. Do příkazového řádku zadejte

   ```console
   gpupdate /force
   ```

### <a name="implement-throttle-rate-limiting-on-session-host-using-powershell"></a>Implementace omezení míry omezení pro hostitele relací pomocí prostředí PowerShell

Míru omezení pro ShortPath RDP můžete nastavit pomocí rutiny PowerShellu níže:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="next-steps"></a>Další kroky

* Další informace o požadavcích na šířku pásma pro virtuální počítač s Windows najdete v článku [Principy požadavků na šířku pásma protokol RDP (Remote Desktop Protocol) (RDP) pro virtuální počítač s Windows](rdp-bandwidth.md).
* Další informace o připojení k síti virtuálních počítačů s Windows najdete v tématu [Principy připojení k síti virtuálních počítačů s Windows](network-connectivity.md).
* Pokud chcete začít se službou QoS (Quality of Service) pro virtuální počítače s Windows, přečtěte si téma [implementace technologie QoS (Quality of Service) pro virtuální počítače s Windows](rdp-quality-of-service-qos.md).
