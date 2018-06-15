---
title: Upravte konfiguraci zařízení řady StorSimple 8000 | Microsoft Docs
description: Popisuje, jak používat službu StorSimple Manager zařízení změna konfigurace zařízení StorSimple, která již byla nasazena.
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
ms.openlocfilehash: 13ff24c24a881297775fa5f65821e53ceb83c351
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875155"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Změňte konfiguraci zařízení StorSimple pomocí služby StorSimple Manager zařízení

## <a name="overview"></a>Přehled

Portál Azure **nastavení zařízení** tématu **nastavení** okno obsahuje všechny parametry zařízení, které můžete změnit konfiguraci na zařízení StorSimple, který je spravovaný nástrojem service Manager zařízení StorSimple . Tento kurz popisuje, jak můžete použít **nastavení** okně můžete provádět následující úlohy úrovni zařízení:

* Změnit popisný název zařízení
* Upravit nastavení času zařízení
* Přiřadit sekundární server DNS
* Upravit síťová rozhraní
* Swap – nebo změna přiřazení IP adresy

## <a name="modify-device-friendly-name"></a>Změnit popisný název zařízení

Na portálu Azure můžete změnit název zařízení a přiřaďte ho jedinečný popisný název podle svého výběru. Použití **obecné nastavení** okno na vašem zařízení změnit popisný název zařízení. Popisný název může obsahovat libovolné znaky a může být maximálně 64 znaků.

> [!NOTE] 
> Název zařízení na portálu Azure můžete upravit pouze před dokončením instalačního programu zařízení. Po dokončení minimální instalace zařízení nemůže změnit název zařízení.

![Název zařízení obecné nastavení](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Zařízení StorSimple, která je připojena ke službě StorSimple Manager zařízení je přiřazen výchozí název. Výchozí název odráží obvykle sériové číslo zařízení. Výchozí název zařízení, která je 15 znaků, jako je například 8600-SHX0991003G44HT, například označuje následující:

* **8600** – označuje model zařízení.
* **TVX** – označuje místo výroby.
* **0991003** -označuje konkrétní produkt.
* **G44HT**-posledních 5 číslic se zvýší, chcete-li vytvořit jedinečný sériová čísla. Toto nemusí být po sobě jdoucích sadu.

## <a name="modify-device-description"></a>Upravit popis zařízení

Použití **obecné nastavení** okno na vašem zařízení a upravit popis zařízení.

![Popis zařízení v obecné nastavení](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Popis zařízení obvykle pomáhá identifikovat vlastníka a fyzické umístění zařízení. Pole popisu musí obsahovat méně než 256 znaků.

## <a name="modify-time-settings"></a>Upravit nastavení času

Zařízení musí synchronizovat čas pro ověřování u vašeho poskytovatele služeb úložiště v cloudu. Použití **obecné nastavení** okno na vašem zařízení a upravit nastavení času zařízení.

![Popis zařízení v obecné nastavení](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 V rozevíracím seznamu vyberte časové pásmo. Můžete zadat až dva servery protokolu NTP (Network Time):

 - **Primární server NTP** -konfigurace je povinná a je zadána při použití prostředí Windows PowerShell pro StorSimple pro konfiguraci zařízení. Můžete zadat výchozí nastavení systému Windows Server **time.windows.com** jako NTP server. Konfigurace primárního serveru NTP prostřednictvím portálu Azure můžete zobrazit, ale musíte použít rozhraní Windows PowerShell ho změnit. Použití `Set-HcsNTPClientServerAddress` rutinu pro změnu primární server NTP vašeho zařízení. Další informace najdete v tématu synxtax pro rutinu [Set-HcsNTPClientServerAddress] (https://technet.microsoft.com/library/dn688138.aspx).

- **Sekundární server NTP** -konfigurace je volitelné. Na portálu můžete použít ke konfiguraci sekundární server NTP.

Při konfiguraci serveru NTP, ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na Internet. Při zadávání veřejného serveru NTP, musí se ujistěte, že vaše síťové brány firewall a dalších zařízení zabezpečení jsou nakonfigurované na povolit protokol NTP provoz do a z vnější síti. Pokud přenos dat NTP obousměrného není povolena, je nutné použít interní server NTP (řadič domény systému Windows poskytuje funkce). Pokud vaše zařízení nelze synchronizovat čas, nemusí být schopen komunikovat s poskytovatelem úložiště cloudu.

Chcete-li zobrazit seznam veřejné servery NTP, přejděte na [webové servery NTP](http://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Co se stane, když zařízení je nasazena v jiném časovém pásmu?

Pokud zařízení je nasazena v jiném časovém pásmu, změní se na časové pásmo zařízení. Vzhledem k tomu, že všechny zásady zálohování použít časové pásmo zařízení, budou automaticky upravit zásady zálohování v souladu s novým časovým pásmem. Není vyžadován žádný zásah uživatele.

## <a name="modify-dns-settings"></a>Změna nastavení DNS

DNS server se používá, když se zařízení pokusí o komunikaci se svým poskytovatelem služeb úložiště v cloudu. Použití **nastavení síťového** okno na zařízení k zobrazení a změně nakonfigurovaných nastavení DNS. 

![Nastavení DNS v nastavení sítě](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Pro vysokou dostupnost musíte nakonfigurovat primární a sekundární servery DNS při nasazení počáteční zařízení.

**Primární server DNS** -nejprve zadat při počátečním nastavení primární server DNS pomocí Windows Powershellu pro StorSimple. Primární server DNS pouze pomocí rozhraní Windows PowerShell můžete znovu nakonfigurovat. Použití `Set-HcsDNSClientServerAddress` rutiny změnit primární server DNS vašeho zařízení. Další informace, přejděte na synxtax pro [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) rutiny.

**Sekundární server DNS** – Pokud chcete upravit sekundární server DNS, použijte `Set-HcsDNSClientServerAddress` rutiny v prostředí Windows PowerShell rozhraní zařízení nebo **nastavení síťového** okno zařízení StorSimple na portálu Azure.

Pokud chcete upravit sekundární server DNS na portálu Azure, proveďte následující kroky.

1. Přejděte do služby Správce zařízení StorSimple. Seznam zařízení vyberte a klikněte na zařízení.

2. V **nastavení** okno, přejděte na **nastavení zařízení > sítě**. Otevře **nastavení síťového** okno. Klikněte na tlačítko **nastavení DNS** dlaždici. Upravte sekundární IP adresa serveru DNS.

    ![Upravit sekundární adderss IP serveru DNS](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Na panelu příkazů klikněte na **Uložit** a po zobrazení výzvy k potvrzení, klikněte na tlačítko **OK**.

    ![Uložte a potvrďte změny](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Upravit síťová rozhraní

Vaše zařízení má šest zařízení síťových rozhraní, čtyři z nich jsou 1gbe a dvě z nich jsou 10 GbE. Tato rozhraní jsou označeny jako DATA 0 – DATA 5. DATA 0, 1 dat, DATA 4 a 5 dat jsou 1gbe, zatímco 10 GbE síťová rozhraní DATA 2 a DATA 3.

Použití **nastavení síťového** okno a nakonfigurujte všechny rozhraní, který se má použít.

![Konfigurace síťových rozhraní pomocí nastavení sítě](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Abyste zajistili vysokou dostupnost, doporučujeme mít aspoň dvě rozhraní iSCSI a dvě rozhraní povolenou podporu cloudu na zařízení. Jsme doporučujeme, ale nevyžadují zakázat nepoužívané rozhraní.

Pro každé síťové rozhraní zobrazí se následující parametry:

* **Rychlost** – není uživatelsky konfigurovatelného parametr. DATA 0, 1 dat, DATA 4 a 5 dat jsou vždy 1 GbE, zatímco 10 GbE rozhraní DATA 2 a DATA 3.
  
  > [!NOTE]
  > Rychlost a duplexní režim jsou vždy automaticky vyjednal. Rámce typu Jumbo nejsou podporovány.
  
* **Stav rozhraní** – rozhraní můžete povolit nebo zakázat. Pokud je povoleno, zařízení se pokusí použít rozhraní. Doporučujeme, aby byl povolen pouze rozhraní, které jsou připojené k síti a použít. Zakažte všechny rozhraní, které nepoužíváte.
* **Typ rozhraní** – tento parametr umožňuje izolovat přenosy iSCSI od provozu cloudu úložiště. Tento parametr může být jedna z následujících akcí:
  
  * **Cloud povolené** – Pokud je povoleno, zařízení bude používat toto rozhraní ke komunikaci s cloudem.
  * **iSCSI povoleno** – Pokud je povoleno, zařízení bude používat toto rozhraní ke komunikaci s hostitelem iSCSI.
    
    Doporučujeme, abyste izolovat přenosy iSCSI od provozu cloudu úložiště. Všimněte si, že pokud je váš hostitel ve stejné podsíti jako zařízení, není potřeba přiřadit bránu; ale pokud váš hostitel je v jiné podsíti, než zařízení, musíte přiřadit bránu.
* **IP adresa** – Pokud nakonfigurujete všechny síťových rozhraní, je nutné nakonfigurovat virtuální IP (VIP). To může být IPv4 nebo IPv6 nebo obojí. Rodiny adres IPv4 a IPv6 jsou podporovány pro síťová rozhraní zařízení. Při použití protokolu IPv4, zadejte IP adresu 32-bit (*xxx.xxx.xxx.xxx*) v notaci tečkou decimal. Pokud používáte IPv6, jednoduše zadejte předponu 4 číslice a adresu 128-bit budou automaticky generovány pro vaše zařízení síťové rozhraní na základě této předpony.
* **Podsíť** – to odkazuje na masku podsítě a je nakonfigurován pomocí rozhraní Windows PowerShell.
* **Brána** – to je výchozí brána, který se použije tímto rozhraním při jeho pokusu o komunikaci s uzly, které nejsou v rámci stejné adresní prostor IP adres (podsítě). Výchozí brány musí být stejné adresního prostoru (podsítě) jako rozhraní IP adresu, určeného masku podsítě.
* **Pevná IP adresa** – toto pole je dostupné pouze tehdy, když nakonfigurujete DATA 0 rozhraní. Pro operace, například aktualizace nebo řešení potíží s zařízení musíte připojit přímo k řadiči zařízení. Pevnou IP adresu můžete použít pro přístup k aktivní a pasivní řadiče na vašem zařízení.

> [!NOTE]
> * Chcete-li zajistit správnou funkci, ověřte rychlost rozhraní a duplexní režim na přepínači, která je každé rozhraní zařízení připojena k. Přepínač rozhraní by měl buď vyjednat s nebo se dá nakonfigurovat pro adaptéry Gigabit Ethernet (1000 Mb/s) a být plně duplexní. Rozhraní pracující na nižší rychlost nebo v poloduplexní způsobí problémy s výkonem.
> * Chcete-li minimalizovat narušení a výpadky, doporučujeme, abyste povolili portfast na všechny porty přepínače, které síťové rozhraní iSCSI vašeho zařízení se připojují k. Tím bude zajištěno, že v případě selhání můžete rychle vytvořit připojení k síti.

### <a name="configure-data-0"></a>Konfigurovat DATA 0

Rozhraní DATA 0 má povolenou podporu cloudu ve výchozím nastavení. Při konfiguraci DATA 0, jsou také potřeba nakonfigurovat dva pevné IP adresy, jednu pro každý kontroler. Tyto pevné IP adresy lze získat přímo přístup k řadiče zařízení a jsou užitečné při instalaci aktualizací na zařízení, pro uvolňování paměti fungovalo správně, nebo při přístupu k řadičům pro účely odstraňování potíží.

Pevné IP řadiče prostřednictvím okně DATA 0 nastavení můžete změnit konfiguraci.

![Nakonfigurujte rozhraní sítě - DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Pevné IP adresy pro řadič se používají k doručování aktualizací do zařízení a pro algoritmy recyklace místa (uvolňování paměti) správně fungovat. Pevné IP adresy proto musí být směrovatelné a schopné připojení k internetu.

### <a name="configure-data-1---data-5"></a>Konfigurovat DATA 1 - DATA 5

Pro DATA 1 - 5 síťová rozhraní DATA, můžete nakonfigurovat všechna nastavení sítě, jak je znázorněno na následujícím snímku obrazovky:

![Nakonfigurujte síťová rozhraní DATA 1 - 5 dat](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Swap – nebo změna přiřazení IP adresy

V současné době Pokud žádné síťové rozhraní na řadiči je přiřazenou VIP, která je používána (ve stejném zařízení nebo jiné zařízení v síti), pak řadičem bude převzetí služeb při selhání. Swap nebo změnu přiřazení virtuální IP adresy pro síťové rozhraní zařízení, je nutné postupovat podle správné postupu jako můžete vytvořit duplicitní IP situaci.

Proveďte následující kroky pro odkládacího souboru nebo změnu přiřazení virtuální IP adresy pro některý z rozhraní sítě:

#### <a name="to-reassign-ips"></a>Přiřazení IP adresy

1. Zrušte IP adresu pro obě rozhraní.
2. Po IP adresy jsou vymazány, přiřaďte nové IP adresy do příslušných rozhraní.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [konfigurace funkce MPIO pro zařízení StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Zjistěte, jak [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

