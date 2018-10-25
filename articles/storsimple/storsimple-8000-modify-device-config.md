---
title: Úprava konfigurace zařízení StorSimple 8000 series | Dokumentace Microsoftu
description: Popisuje způsob použití služby Správce zařízení StorSimple k překonfigurování zařízení StorSimple, která již byla nasazena.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 727cf523f2a505729377f36738657fc5489134a7
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025986"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Upravit konfiguraci zařízení StorSimple pomocí služby Správce zařízení StorSimple

## <a name="overview"></a>Přehled

Na webu Azure portal **nastavení zařízení** tématu **nastavení** okno obsahuje všechny parametry zařízení, které můžete změnit konfiguraci na zařízení StorSimple, která jsou spravována služby Správce zařízení StorSimple . Tento kurz vysvětluje, jak můžete použít **nastavení** okno a provádět následující úlohy zařízení:

* Změnit popisný název zařízení
* Upravit nastavení času zařízení
* Přiřadit sekundární server DNS
* Změnit síťová rozhraní
* Zaměnit nebo změna přiřazení IP adresy

## <a name="modify-device-friendly-name"></a>Změnit popisný název zařízení

Na webu Azure portal můžete změnit název zařízení a přiřaďte ho jedinečný popisný název, podle vašeho výběru. Použití **obecné nastavení** okno na vašem zařízení změnit popisný název zařízení. Popisný název může obsahovat libovolné znaky a může být maximálně 64 znaků.

> [!NOTE] 
> Název zařízení na portálu Azure portal můžete upravit jenom před dokončením instalačního programu zařízení. Po dokončení minimální instalace zařízení nelze změnit název zařízení.

![Název zařízení v obecných nastavení](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Zařízení StorSimple, která je připojená ke službě Správce zařízení StorSimple je přiřazen výchozí název. Výchozí název obvykle odráží sériové číslo zařízení. Například výchozí název zařízení, která je 15 znaků, jako je například 8600 – SHX0991003G44HT označuje následující:

* **8600** – označuje model zařízení.
* **TVX** – označuje místo výroby.
* **0991003** – označuje konkrétní produkt.
* **G44HT**– posledních 5 číslic jsou zvětšeny pro vytvoření jedinečnými sériovými čísly. To nemusí být sada sekvenční.

## <a name="modify-device-description"></a>Upravit popis zařízení

Použití **obecné nastavení** okno na vašem zařízení změnit popis zařízení.

![Popis zařízení v části Obecné nastavení](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Popis zařízení obvykle pomáhá identifikovat vlastníka a fyzické umístění zařízení. Pole popisu musí obsahovat méně než 256 znaků.

## <a name="modify-time-settings"></a>Upravit nastavení času

Zařízení musí synchronizovat čas, aby bylo možné ověřit pomocí poskytovatele cloudových služeb úložiště. Použití **obecné nastavení** okno na vašem zařízení můžete upravit nastavení času zařízení.

![Popis zařízení v části Obecné nastavení](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Z rozevíracího seznamu vyberte časové pásmo. Můžete zadat až dva servery Protokol NTP (Network Time):

 - **Primární server NTP** – konfigurace je povinná a je zadaný při konfiguraci zařízení pomocí Windows Powershellu pro StorSimple. Můžete určit výchozí systému Windows Server **time.windows.com** jako váš server NTP. Primární konfiguraci serveru NTP. na webu Azure portal můžete zobrazit, ale musíte použít rozhraní Windows PowerShell ho změnit. Použití `Set-HcsNTPClientServerAddress` rutiny změnit primární NTP server vašeho zařízení. Další informace najdete v části synxtax pro [Set-HcsNTPClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) rutiny.

- **Sekundární server NTP** – konfigurace je volitelný. Na portálu můžete použít ke konfiguraci sekundární server NTP.

Při konfiguraci serveru NTP, ujistěte se, že vaše síť povoluje dat NTP z vašeho datového centra k Internetu. Při zadáním veřejného serveru NTP, musíte se ujistit, že brány firewall sítě a dalších zařízení zabezpečení umožňují do a z vnější síti přenos dat NTP. Pokud není povolený obousměrný přenos dat NTP, je nutné použít interní server NTP (Tato funkce poskytuje řadič domény Windows). Pokud zařízení nemůže synchronizovat čas, nemusí být schopen komunikovat se svým poskytovatelem cloud storage.

Chcete-li zobrazit seznam veřejných servery NTP, přejděte [webové servery NTP](http://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Co se stane, pokud se zařízení nasazuje v jiném časovém pásmu?

Pokud se zařízení nasazuje v jiném časovém pásmu, časové pásmo zařízení změní. Vzhledem k tomu, že všechny zásady zálohování použít časové pásmo zařízení, budou automaticky upravit zásady zálohování v souladu s novým časovým pásmem. Není vyžadován žádný zásah uživatele.

## <a name="modify-dns-settings"></a>Změna nastavení DNS

DNS server se používá při zařízení pokusí se komunikovat se svým poskytovatelem služeb cloudových úložišť. Použití **nastavení sítě** okno na vašem zařízení zobrazovat a upravovat nakonfigurovaná nastavení DNS. 

![Nastavení DNS v nastavení sítě](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Pro zajištění vysoké dostupnosti je potřeba nakonfigurovat primární a sekundární servery DNS během nasazování počáteční zařízení.

**Primární server DNS** – při počátečním nastavení nejprve zadat primární DNS server pomocí prostředí Windows PowerShell pro StorSimple. Primární server DNS jenom přes rozhraní Windows PowerShell můžete znovu nakonfigurovat. Použití `Set-HcsDNSClientServerAddress` rutiny změnit primární server DNS vašeho zařízení. Další informace najdete v části synxtax pro [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) rutiny.

**Sekundární server DNS** – Chcete-li upravit sekundární server DNS, použijte `Set-HcsDNSClientServerAddress` rutiny v prostředí Windows PowerShell rozhraní zařízení nebo **nastavení sítě** okno zařízení StorSimple na webu Azure Portal.

Pokud chcete upravit sekundární server DNS na webu Azure portal, postupujte následovně.

1. Přejděte do služby Správce zařízení StorSimple. Ze seznamu zařízení vyberte a klikněte na vašem zařízení.

2. V **nastavení** okno, přejděte na **nastavení zařízení > sítě**. Otevře **nastavení sítě** okno. Klikněte na tlačítko **nastavení DNS** dlaždici. Úprava IP adresy sekundární server DNS.

    ![Upravit sekundární adderss IP serveru DNS](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Na panelu příkazů klikněte na tlačítko **Uložit** a po zobrazení výzvy k potvrzení, klikněte na tlačítko **OK**.

    ![Uložte a potvrďte změny](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Změnit síťová rozhraní

Vaše zařízení má šest zařízení síťových rozhraní, čtyři z nich jsou 1 GbE a dva z nich jsou 10 GbE. Tato rozhraní jsou označeny jako DATA 0 – DATA 5. DATA 0, DATA 1, DATA 4 a DATA 5 jsou 1 GbE, zatímco rozhraní sítí 10 GbE DATA 2 a DATA 3.

Použití **nastavení sítě** okno a nakonfigurujte všechna rozhraní, který se má použít.

![Konfigurace síťových rozhraní pomocí nastavení sítě](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

K zajištění vysoké dostupnosti, doporučujeme mít aspoň dvě rozhraní iSCSI a dvě rozhraní povolenou podporu cloudu na zařízení. Jsme doporučujeme ale nevyžadují zakázat nepoužívané rozhraní.

Pro každé síťové rozhraní se zobrazí následující parametry:

* **Rychlost** – není uživatelem konfigurovatelné parametr. DATA 0, DATA 1, DATA 4 a DATA 5 jsou vždy 1 GbE, zatímco 10 GbE rozhraní DATA 2 a DATA 3.
  
  > [!NOTE]
  > Rychlost a duplexní se vždy automaticky-vyjednají během komunikace. Rámce typu Jumbo nepodporuje.
  
* **Stav rozhraní** – rozhraní můžete povolit nebo zakázat. Pokud je povoleno, zařízení se pokusí použít rozhraní. Doporučujeme, aby byly povoleny pouze rozhraní, které jsou připojené k síti a použít. Zakážete všechna rozhraní, které nepoužíváte.
* **Typ rozhraní** – tento parametr umožňuje izolovat přenosy iSCSI z provozu úložiště cloudu. Tento parametr může být jeden z následujících akcí:
  
  * **Cloud je povolený** – při povolené, zařízení bude používat toto rozhraní ke komunikaci s cloudem.
  * **povolené iSCSI** – při povolené, zařízení bude používat toto rozhraní ke komunikaci s hostitelem iSCSI.
    
    Doporučujeme, abyste izolovat přenosy iSCSI z provozu úložiště cloudu. Všimněte si také, že pokud je váš hostitel ve stejné podsíti jako zařízení, není potřeba přiřadit bránu; Nicméně pokud váš hostitel je v jiné podsíti, než zařízení, musíte přiřadit bránu.
* **IP adresa** – při konfiguraci libovolné ze síťových rozhraní, je nutné nakonfigurovat virtuální IP adresy (VIP). To může být IPv4 nebo IPv6 nebo obojí. Rodiny adres IPv4 a IPv6 jsou podporovány pro síťová rozhraní zařízení. Při použití protokolu IPv4, zadejte IP adresu 32-bit (*xxx.xxx.xxx.xxx*) v tečkou desítkovém zápisu. Pokud používáte IPv6, jednoduše zadat předponu 4 číslice a 128bitové adresy automaticky se vygeneruje pro zařízení síťového rozhraní na základě této předpony.
* **Podsíť** – to odkazuje na masku podsítě a je nakonfigurovaný přes rozhraní Windows PowerShell.
* **Brána** – to je výchozí brána, který by měl být použit toto rozhraní, když se pokoušet o komunikaci s uzly, které nejsou v rámci stejné adresní prostor IP adres (podsítě). Výchozí brána musí být ve stejném adresním prostoru (stejná podsíť) jako rozhraní IP adresa, podle masky podsítě.
* **Pevné IP adresy** – toto pole je dostupné pouze tehdy, když nakonfigurujete DATA 0 rozhraní. Pro operace, třeba aktualizace nebo odstraňování zařízení budete muset připojit přímo k řadiči zařízení. Pevná IP adresa slouží pro přístup k aktivní a pasivní kontroler na vašem zařízení.

> [!NOTE]
> * Chcete-li zajistit správnou funkci, ověřte rychlosti rozhraní a duplexní na každé zařízení rozhraní připojené k přepínači. Přepínač rozhraní by měl buď vyjednat s nebo nakonfigurovat adaptéry Gigabit Ethernet (1 000 MB/s) a být plně duplexní. Rozhraní pracující na nižší rychlostí nebo v poloduplexní způsobí problémy s výkonem.
> * Chcete-li minimalizovat narušení a výpadky, doporučujeme, abyste povolili portfast na všechny porty přepínače, které se síťové rozhraní iSCSI, zařízení budou připojovat k. Tím se zajistí, že v případě převzetí služeb můžete rychle vytvořit připojení k síti.

### <a name="configure-data-0"></a>Konfigurovat DATA 0

Rozhraní DATA 0 má povolenou podporu cloudu ve výchozím nastavení. Při konfiguraci DATA 0, je také potřeba nakonfigurovat dva pevné IP adresy, jeden pro každý kontroler. Tyto pevné IP adresy je možné získat přímo přístup k řadiči zařízení a jsou užitečné při instalaci aktualizací na zařízení, pro uvolnění paměti fungovalo správně, nebo při přístupu k řadiči pro účely řešení potíží s.

Pevné IP řadiče prostřednictvím okna DATA 0 nastavení můžete změnit konfiguraci.

![Nakonfigurujte síťové rozhraní – DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Pevné IP adresy pro řadič slouží k doručování aktualizací do zařízení a algoritmů recyklace místa (uvolňování) fungovala správně. Pevné IP adresy proto musí být směrovatelné a schopné připojení k internetu.

### <a name="configure-data-1---data-5"></a>Konfigurace dat 1 - DATA 5

Pro DATA 1 - 5 síťová rozhraní DATA, můžete nakonfigurovat všechna nastavení sítě, jak je znázorněno na následujícím snímku obrazovky:

![Nakonfigurujte síťová rozhraní DATA 1 - 5 dat](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Zaměnit nebo změna přiřazení IP adresy

V současné době Pokud všech síťových rozhraních v řadiči přiřadí virtuální IP adresu, která se používá (stejné zařízení nebo jiné zařízení v síti), pak kontroleru převezme. Pokud vyměnit nebo změna přiřazení virtuálních IP adres pro síťové rozhraní zařízení, je třeba dodržet správný postup jako můžete vytvořit duplicitní IP situaci.

Proveďte následující kroky odkládacího souboru, nebo znovu přiřadit virtuální IP adresy pro některý ze síťových rozhraní:

#### <a name="to-reassign-ips"></a>Přiřazení IP adresy

1. Vymažte IP adresu pro obě rozhraní.
2. Po IP adresy jsou vymazány, přiřadíte nové IP adresy pro příslušné rozhraní.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [konfigurace funkce MPIO pro zařízení StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Zjistěte, jak [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

