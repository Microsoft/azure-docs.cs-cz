---
title: Úprava konfigurace zařízení řady StorSimple 8000 | Microsoft Docs
description: Popisuje, jak používat službu StorSimple Správce zařízení k překonfigurování zařízení StorSimple, které už je nasazené.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 5eb5c351462279fe5c4f790e052f73201d211cfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94961171"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Změna konfigurace zařízení StorSimple pomocí služby StorSimple Správce zařízení

## <a name="overview"></a>Přehled

Část Azure Portal **nastavení zařízení** v okně **Nastavení** obsahuje všechny parametry zařízení, které můžete znovu nakonfigurovat na zařízení StorSimple, které spravuje služba StorSimple Správce zařízení. V tomto kurzu se dozvíte, jak můžete pomocí okna **Nastavení** provádět následující úlohy na úrovni zařízení:

* Upravit popisný název zařízení
* Úprava nastavení času zařízení
* Přiřazení sekundárního serveru DNS
* Upravit síťová rozhraní
* Prohození nebo změna přiřazení IP adres

## <a name="modify-device-friendly-name"></a>Upravit popisný název zařízení

Pomocí Azure Portal můžete změnit název zařízení a přiřadit mu jedinečný popisný název podle vašeho výběru. Pomocí okna **Obecné nastavení** na zařízení upravte popisný název zařízení. Popisný název může obsahovat libovolné znaky a může mít maximálně 64 znaků.

> [!NOTE] 
> Název zařízení můžete změnit jenom v Azure Portal před dokončením instalace zařízení. Až se minimální instalace zařízení dokončí, nemůžete změnit název zařízení.

![Název zařízení v obecných nastaveních](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

K StorSimple zařízení, které je připojené ke službě StorSimple Správce zařízení, se přiřadí výchozí název. Výchozí název obvykle odráží sériové číslo zařízení. Například výchozí název zařízení, který je delší než 15 znaků, například 8600-SHX0991003G44HT, označuje následující:

* **8600**  – označuje model zařízení.
* **SHX** – označuje pracoviště výroby.
* **0991003** – označuje určitý produkt.
* **G44HT**– při posledních 5 číslicích se zvýší a vytvoří se jedinečná sériová čísla. Nemusí se jednat o sekvenční sadu.

## <a name="modify-device-description"></a>Změnit popis zařízení

Popis zařízení upravíte pomocí okna **Obecné nastavení** na vašem zařízení.

![Popis zařízení v obecných nastaveních](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Popis zařízení obvykle pomáhá identifikovat vlastníka a fyzické umístění zařízení. Pole popisu musí obsahovat méně než 256 znaků.

## <a name="modify-time-settings"></a>Úprava nastavení času

Vaše zařízení musí synchronizovat čas, aby se mohl ověřit s vaším poskytovatelem cloudových služeb úložiště. Pomocí okna **Obecné nastavení** v zařízení upravte nastavení času zařízení.

![Popis zařízení v obecných nastaveních 2](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 V rozevíracím seznamu vyberte své časové pásmo. Můžete zadat až dva servery protokolu NTP (Network Time Protocol):

 - **Primární server NTP** – konfigurace je povinná a je zadaná při použití Windows PowerShell pro StorSimple ke konfiguraci zařízení. Jako server NTP můžete zadat výchozí **time.Windows.com** Windows serveru. Konfiguraci primárního serveru NTP můžete zobrazit pomocí Azure Portal, k jeho změně ale musíte použít rozhraní Windows PowerShell. Pomocí `Set-HcsNTPClientServerAddress` rutiny upravte primární server NTP vašeho zařízení. Další informace najdete v syntaxi pro rutinu [set-HcsNTPClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) .

- **Sekundární server NTP** – konfigurace je volitelná. Portál můžete použít ke konfiguraci sekundárního serveru NTP.

Při konfiguraci serveru NTP zajistěte, aby síť umožňovala předávání provozu NTP z vašeho datacentra na Internet. Když zadáte veřejný server NTP, musíte zajistit, aby byly brány firewall sítě a další zabezpečovací zařízení nakonfigurované tak, aby umožňovaly přenos dat NTP do vnější sítě a z ní. Pokud není přenos s obousměrným protokolem NTP povolený, musíte použít interní server NTP (tuto funkci poskytuje řadič domény systému Windows). Pokud vaše zařízení nemůže synchronizovat čas, nemusí být schopné komunikovat s vaším poskytovatelem cloudového úložiště.

Seznam veřejných serverů NTP zobrazíte tak, že přejdete na [Web servery NTP](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Co se stane, když se zařízení nasadí v jiném časovém pásmu?

Pokud je zařízení nasazené v jiném časovém pásmu, změní se časové pásmo zařízení. Vzhledem k tom, že všechny zásady zálohování používají časové pásmo zařízení, zásady zálohování se automaticky upraví podle nového časového pásma. Není vyžadován žádný zásah uživatele.

## <a name="modify-dns-settings"></a>Úprava nastavení DNS

Server DNS se používá, když se vaše zařízení pokusí komunikovat s vaším poskytovatelem cloudového úložiště. V okně **nastavení sítě** na zařízení můžete zobrazit a upravit nakonfigurovaná nastavení DNS. 

![Nastavení DNS v nastavení sítě](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Pro zajištění vysoké dostupnosti je nutné při počátečním nasazení zařízení nakonfigurovat primární a sekundární servery DNS.

**Primární server DNS** – při počátečním nastavení použijte k prvnímu určení primárního serveru DNS Windows PowerShell pro StorSimple. Primární server DNS můžete překonfigurovat jenom přes rozhraní Windows PowerShell. Pomocí `Set-HcsDNSClientServerAddress` rutiny upravte primární server DNS vašeho zařízení. Další informace najdete v syntaxi pro rutinu [set-HcsDNSClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) .

**Sekundární server DNS** – Chcete-li změnit sekundární server DNS, použijte `Set-HcsDNSClientServerAddress` rutinu v rozhraní Windows PowerShell okna zařízení nebo **nastavení sítě** zařízení StorSimple v Azure Portal.

Pokud chcete změnit sekundární server DNS v Azure Portal, proveďte následující kroky.

1. Přejděte do služby Správce zařízení StorSimple. V seznamu zařízení vyberte a klikněte na své zařízení.

2. V okně **Nastavení** přejdete na **nastavení zařízení > síť**. Otevře se okno **nastavení sítě** . Klikněte na dlaždici **nastavení DNS** . Upravte IP adresu sekundárního serveru DNS.

    ![Úprava IP adresy sekundárního serveru DNS](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Na panelu příkazů klikněte na **Uložit** a po zobrazení výzvy k potvrzení klikněte na **OK**.

    ![Uložit a potvrdit změny](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Upravit síťová rozhraní

Vaše zařízení má šest síťových rozhraní zařízení, čtyři z nich jsou 1 GbE a dva z nich jsou 10 GbE. Tato rozhraní jsou označena jako DATA 0 – DATA 5. DATA 0, DATA 1, DATA 4 a DATA 5 jsou 1 GbE, zatímco DATA 2 a DATA 3 jsou síťová rozhraní 10 GbE.

Pomocí okna **nastavení sítě** můžete nakonfigurovat každé rozhraní, které se má použít.

![Konfigurace síťových rozhraní prostřednictvím nastavení sítě](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Abychom zajistili vysokou dostupnost, doporučujeme mít v zařízení aspoň dvě rozhraní iSCSI a dvě cloudová rozhraní. Doporučujeme, ale nemusíte zakázat nepoužívaná rozhraní.

Pro každé síťové rozhraní se zobrazí následující parametry:

* **Rychlost** – nejedná se o uživatelsky konfigurovatelný parametr. DATA 0, DATA 1, DATA 4 a DATA 5 jsou vždycky 1 GbE, zatímco DATA 2 a DATA 3 jsou rozhraní 10 GbE.
  
  > [!NOTE]
  > Rychlost a duplexní přenos se vždycky automaticky vyjednávají. Rámce typu Jumbo nejsou podporovány.
  
* **Stav rozhraní** – rozhraní lze povolit nebo zakázat. Pokud je povoleno, zařízení se pokusí použít rozhraní. Doporučujeme, aby byla povolená jenom ta rozhraní, která jsou připojená k síti a použitá. Zakažte všechna rozhraní, která nepoužíváte.
* **Typ rozhraní** – tento parametr umožňuje izolovat provoz iSCSI od provozu cloudového úložiště. Tento parametr může být jeden z následujících:
  
  * **Povolený Cloud** – Pokud je povolený, zařízení bude toto rozhraní používat ke komunikaci s cloudem.
  * **povolené iSCSI** – Pokud je povolené, zařízení bude toto rozhraní používat ke komunikaci s hostitelem iSCSI.
    
    Doporučujeme izolovat provoz iSCSI od provozu cloudového úložiště. Všimněte si také, že pokud je hostitel ve stejné podsíti jako vaše zařízení, nemusíte přiřazovat bránu. Pokud je ale hostitel v jiné podsíti než vaše zařízení, budete muset bránu přiřadit.
* **IP adresa** – když nakonfigurujete jakákoli síťová rozhraní, musíte nakonfigurovat virtuální IP adresu (VIP). Může to být protokol IPv4 nebo IPv6 nebo obojí. Pro síťová rozhraní zařízení jsou podporované i rodiny adres IPv4 i IPv6. Při použití protokolu IPv4 zadejte IP adresu 32 (*xxx.xxx.xxx.xxx*) v desítkovém zápisu s tečkou. Při použití protokolu IPv6 jednoduše zadejte předponu o úrovni 4 číslice a na základě této předpony bude pro síťové rozhraní zařízení automaticky vygenerována adresa 128.
* **Podsíť** – Tato položka odkazuje na masku podsítě a je nakonfigurovaná přes rozhraní Windows PowerShell.
* **Brána** – jedná se o výchozí bránu, kterou má toto rozhraní použít při pokusu o komunikaci s uzly, které nejsou v rámci stejného adresního prostoru IP adres (podsítě). Výchozí brána musí být ve stejném adresním prostoru (podsíti) jako IP adresa rozhraní, jak je určeno maskou podsítě.
* **Pevná IP adresa** – toto pole je k dispozici pouze při konfiguraci rozhraní data 0. Pro operace, jako jsou třeba aktualizace nebo řešení potíží se zařízením, se možná budete muset připojit přímo k řadiči zařízení. Pevná IP adresa se dá použít pro přístup k aktivnímu i pasivnímu řadiči vašeho zařízení.

> [!NOTE]
> * Chcete-li zajistit správnou činnost, ověřte rychlost rozhraní a duplexní režim na přepínači, ke kterému je připojeno každé rozhraní zařízení. Rozhraní přepínače by se měla buď vyjednávat s přepínačem gigabitového Ethernetu (1000 MB/s), nebo musí být nastavená na plně duplexní. Rozhraní provozovaná s pomalejšími rychlostmi nebo v poloduplexním přenosu budou mít za následek problémy s výkonem.
> * Pro minimalizaci výpadků a výpadků doporučujeme povolit portfast na všech portech přepínače, ke kterým se síťové rozhraní iSCSI vašeho zařízení bude připojovat. Tím se zajistí, že se připojení k síti dá v případě převzetí služeb při selhání rychle vytvořit.

### <a name="configure-data-0"></a>Konfigurovat DATA 0

DATA 0 jsou ve výchozím nastavení povolena pro Cloud. Při konfiguraci dat 0 budete taky muset nakonfigurovat dvě pevné IP adresy, jednu pro každý kontroler. Tyto pevné IP adresy lze použít pro přímý přístup k řadičům zařízení a jsou užitečné při instalaci aktualizací do zařízení, aby uvolňování paměti fungovalo správně nebo při přístupu k řadičům pro účely řešení potíží.

Pevné řadiče IP adres můžete znovu nakonfigurovat přes okno nastavení DATA 0.

![Konfigurace síťového rozhraní – DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Pevné IP adresy pro řadič se používají k údržbě aktualizací zařízení a k zajištění správného fungování algoritmů pro recyklaci místa (uvolňování paměti). Pevné IP adresy proto musí být směrovatelné a schopné připojení k internetu.

### <a name="configure-data-1---data-5"></a>Konfigurace dat 1 – DATA 5

Pro síťová rozhraní DATA 1-DATA 5 můžete nakonfigurovat všechna nastavení sítě, jak je znázorněno na následujícím snímku obrazovky:

![Konfigurace síťových rozhraní DATA 1 – DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Prohození nebo změna přiřazení IP adres

Pokud je v současné době každé síťové rozhraní na řadiči přiřazená virtuální IP adresa, která se používá (přes stejné zařízení nebo jiné zařízení v síti), kontroler převezme služby při selhání. Pokud provedete nebo znovu přiřadíte virtuální IP adresy pro síťové rozhraní zařízení, je nutné postupovat podle správného postupu, jak můžete vytvořit duplicitní situaci IP.

Provedením následujících kroků prohodíte nebo znovu přiřadíte virtuální IP adresy pro kterékoli ze síťových rozhraní:

#### <a name="to-reassign-ips"></a>Opětovné přiřazení IP adres

1. Vymažte IP adresu obou rozhraní.
2. Po vymazání IP adres přiřaďte příslušné rozhraní nové IP adresy.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [nakonfigurovat funkci MPIO pro zařízení StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Naučte se [používat službu StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).