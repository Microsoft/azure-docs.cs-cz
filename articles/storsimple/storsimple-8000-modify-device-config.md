---
title: Změna konfigurace zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje, jak pomocí služby StorSimple Device Manager překonfigurovat zařízení StorSimple, které již bylo nasazeno.
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
ms.openlocfilehash: 774f5a73a5fc30352698c0af0c279fbbe488c480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267687"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Pomocí služby StorSimple Device Manager upravte konfiguraci zařízení StorSimple

## <a name="overview"></a>Přehled

Část **Nastavení zařízení** portálu Azure v okně **Nastavení** obsahuje všechny parametry zařízení, které můžete překonfigurovat na zařízení StorSimple, které je spravované službou StorSimple Device Manager. Tento kurz vysvětluje, jak můžete pomocí okna **Nastavení** provádět následující úlohy na úrovni zařízení:

* Změnit popisný název zařízení
* Změna nastavení času zařízení
* Přiřazení sekundárního DNS
* Úprava síťových rozhraní
* Zaměnit nebo znovu přiřadit IP adresy

## <a name="modify-device-friendly-name"></a>Změnit popisný název zařízení

Na webu Azure Portal můžete změnit název zařízení a přiřadit mu jedinečný popisný název podle vašeho výběru. Pomocí okna **Obecné nastavení** v zařízení upravte popisný název zařízení. Popisný název může obsahovat libovolné znaky a může mít maximálně 64 znaků.

> [!NOTE] 
> Název zařízení můžete upravit jenom na webu Azure Portal před dokončením nastavení zařízení. Po dokončení minimálního nastavení zařízení nelze změnit název zařízení.

![Název zařízení v obecném nastavení](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Zařízení StorSimple, které je připojeno ke službě StorSimple Device Manager, má výchozí název. Výchozí název obvykle odráží sériové číslo zařízení. Například výchozí název zařízení, který má 15 znaků, například 8600-SHX0991003G44HT, označuje následující:

* **8600** – Označuje model zařízení.
* **SHX** – Označuje výrobní místo.
* **0991003** - Označuje konkrétní produkt.
* **G44HT**- Posledních 5 číslic se zvětší, aby se vytvořila jedinečná sériová čísla. Nemusí se jedná o sekvenční sadu.

## <a name="modify-device-description"></a>Změna popisu zařízení

Pomocí okna **Obecné nastavení** v zařízení upravte popis zařízení.

![Popis zařízení v obecném nastavení](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Popis zařízení obvykle pomáhá identifikovat vlastníka a fyzické umístění zařízení. Pole popisu musí obsahovat méně než 256 znaků.

## <a name="modify-time-settings"></a>Změna nastavení času

Aby se zařízení ověřilo u poskytovatele služeb cloudového úložiště, musí synchronizovat čas. Pomocí okna **Obecné nastavení** v zařízení upravte nastavení času zařízení.

![Popis zařízení v obecném nastavení](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 V rozevíracím seznamu vyberte časové pásmo. Můžete zadat až dva servery NTP (Network Time Protocol):

 - **Primární server NTP** – konfigurace je vyžadována a je určena při konfiguraci zařízení pomocí prostředí Windows PowerShell pro StorSimple. Jako server NTP můžete zadat výchozí **time.windows.com** systému Windows Server. Primární konfiguraci serveru NTP můžete zobrazit na webu Azure Portal, ale ke změně je nutné použít rozhraní prostředí Windows PowerShell. Pomocí `Set-HcsNTPClientServerAddress` rutiny upravte primární server NTP vašeho zařízení. Další informace naleznete v syntaxi rutiny [Set-HCSNTPClientServerAddress.](https://technet.microsoft.com/library/dn688138.aspx)

- **Sekundární server NTP** – konfigurace je volitelná. Portál můžete použít ke konfiguraci sekundárního serveru NTP.

Při konfiguraci serveru NTP se ujistěte, že síť umožňuje přenosntp předávat z datového centra do Internetu. Při zadávání veřejného serveru NTP je nutné zajistit, aby síťové brány firewall a další zabezpečovací zařízení byly nakonfigurovány tak, aby umožňovaly přenosntp do vnější sítě a z ní. Pokud obousměrný provoz NTP není povolen, je nutné použít interní server NTP (tuto funkci poskytuje řadič domény systému Windows). Pokud vaše zařízení nemůže synchronizovat čas, nemusí být schopno komunikovat s poskytovatelem cloudového úložiště.

Seznam veřejných serverů NTP naleznete na [webu ntp serverů](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Co se stane, když je zařízení nasazeno v jiném časovém pásmu?

Pokud je zařízení nasazeno v jiném časovém pásmu, časové pásmo zařízení se změní. Vzhledem k tomu, že všechny zásady zálohování používají časové pásmo zařízení, zásady zálohování se automaticky upraví v souladu s novým časovým pásmem. Není vyžadován žádný zásah uživatele.

## <a name="modify-dns-settings"></a>Změna nastavení DNS

Dns server se používá, když se vaše zařízení pokusí komunikovat s poskytovatelem služeb cloudového úložiště. Pomocí okna **Nastavení sítě** v zařízení můžete zobrazit a upravit nakonfigurovaná nastavení DNS. 

![Nastavení DNS v nastavení sítě](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Chcete-li získat vysokou dostupnost, musíte během počátečního nasazení zařízení nakonfigurovat primární i sekundární servery DNS.

**Primární server DNS** – Pomocí prostředí Windows PowerShell pro StorSimple nejprve určíte primární server DNS během počáteční instalace. Primární server DNS můžete překonfigurovat pouze prostřednictvím rozhraní prostředí Windows PowerShell. Pomocí `Set-HcsDNSClientServerAddress` rutiny upravte primární server DNS zařízení. Další informace naleznete v syntaxi rutiny [Set-HcsDNSClientServerAddress.](https://technet.microsoft.com/library/dn688138.aspx)

**Sekundární server DNS** – chcete-li upravit sekundární `Set-HcsDNSClientServerAddress` server DNS, použijte rutinu v rozhraní prostředí Windows PowerShell zařízení nebo okno **Nastavení sítě** vašeho zařízení StorSimple na webu Azure Portal.

Chcete-li upravit sekundární server DNS na webu Azure Portal, proveďte následující kroky.

1. Přejděte do služby Správce zařízení StorSimple. Ze seznamu zařízení vyberte a klikněte na zařízení.

2. V okně **Nastavení** přejděte na **nastavení zařízení > síť**. Tím se otevře okno **Nastavení sítě.** Klikněte na dlaždici **nastavení DNS.** Upravte adresu IP sekundárního serveru DNS.

    ![Změna adresy IP sekundárního serveru DNS](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Na panelu příkazů klikněte na **Uložit** a po zobrazení výzvy k potvrzení klepněte na tlačítko **OK**.

    ![Uložení a potvrzení změn](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Úprava síťových rozhraní

Vaše zařízení má šest síťových rozhraní zařízení, z nichž čtyři jsou 1 GbE a dva z nich jsou 10 GbE. Tato rozhraní jsou označena jako DATA 0 – DATA 5. DATA 0, DATA 1, DATA 4 a DATA 5 jsou 1 GbE, zatímco DATA 2 a DATA 3 jsou 10 GbE síťová rozhraní.

Pomocí okna **Nastavení sítě** můžete nakonfigurovat každé rozhraní, které má být použito.

![Konfigurace síťových rozhraní pomocí nastavení sítě](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Chcete-li zajistit vysokou dostupnost, doporučujeme mít v zařízení alespoň dvě rozhraní iSCSI a dvě rozhraní s podporou cloudu. Doporučujeme, ale nevyžadují, aby nepoužívaná rozhraní být zakázány.

Pro každé síťové rozhraní jsou zobrazeny následující parametry:

* **Rychlost** – Není uživatel-konfigurovatelný parametr. DATA 0, DATA 1, DATA 4 a DATA 5 jsou vždy 1 GbE, zatímco DATA 2 a DATA 3 jsou 10 GbE rozhraní.
  
  > [!NOTE]
  > Rychlost a duplex jsou vždy automaticky vyjednávány. Rámce Jumbo nejsou podporovány.
  
* **Stav rozhraní** – rozhraní může být povoleno nebo zakázáno. Pokud je povoleno, zařízení se pokusí použít rozhraní. Doporučujeme povolit pouze rozhraní, která jsou připojena k síti a používají se. Zakažte všechna rozhraní, která nepoužíváte.
* **Typ rozhraní** – Tento parametr umožňuje izolovat provoz iSCSI od provozu cloudového úložiště. Tento parametr může být jeden z následujících:
  
  * **Cloud povolen** – pokud je povoleno, zařízení bude používat toto rozhraní ke komunikaci s cloudem.
  * **iSCSI povolena** – pokud je povolena, zařízení bude používat toto rozhraní ke komunikaci s hostitelem iSCSI.
    
    Doporučujeme izolovat provoz iSCSI od provozu cloudového úložiště. Všimněte si také, že pokud je hostitel ve stejné podsíti jako vaše zařízení, nemusíte přiřazovat bránu. Pokud je však váš hostitel v jiné podsíti než vaše zařízení, budete muset přiřadit bránu.
* **IP adresa** – Při konfiguraci libovolného síťového rozhraní je nutné nakonfigurovat virtuální IP adresu (VIP). Může to být IPv4 nebo IPv6 nebo obojí. Pro síťová rozhraní zařízení jsou podporovány rodiny adres IPv4 i IPv6. Při použití protokolu IPv4 zadejte 32bitovou adresu IP (*xxx.xxx.xxx.xxx*) v desítkové notaci. Při použití iPv6 jednoduše zadejte čtyřmístnou předponu a pro síťové rozhraní zařízení na základě této předpony bude automaticky generována 128bitová adresa.
* **Podsíť** – Jedná se o masku podsítě a je nakonfigurována prostřednictvím rozhraní prostředí Windows PowerShell.
* **Brána** – Toto je výchozí brána, která by měla být používána tímto rozhraním při pokusu o komunikaci s uzly, které nejsou ve stejném adresním prostoru IP (podsíť). Výchozí brána musí být ve stejném adresním prostoru (podsíti) jako adresa IP rozhraní, jak je určeno maskou podsítě.
* **Pevná IP adresa** – Toto pole je k dispozici pouze při konfiguraci rozhraní DATA 0. Pro operace, jako jsou aktualizace nebo řešení potíží se zařízením, může být nutné připojit přímo k ovladači zařízení. Pevnou IP adresu lze použít pro přístup k aktivnímu i pasivnímu ovladači na vašem zařízení.

> [!NOTE]
> * Chcete-li zajistit správnou funkci, ověřte rychlost rozhraní a duplexní na přepínači, ke kterému je připojeno každé rozhraní zařízení. Rozhraní přepínačů by měla buď vyjednávat s gigabitovým Ethernetem (1000 Mb/s) nebo by měla být nakonfigurována pro gigabitový Ethernet (1000 Mb/s) a měla by být plně duplexní. Rozhraní pracující při pomalejších rychlostech nebo v poloduplexním přenosu budou mít za následek problémy s výkonem.
> * Chcete-li minimalizovat přerušení a prostoje, doporučujeme povolit portfast na každém z portů přepínače, ke kterému se bude připojovat síťové rozhraní iSCSI vašeho zařízení. Tím zajistíte, že připojení k síti lze rychle navázat v případě převzetí služeb při selhání.

### <a name="configure-data-0"></a>Konfigurace dat 0

DATA 0 je ve výchozím nastavení povolena v cloudu. Při konfiguraci data 0 je také nutné nakonfigurovat dvě pevné ADRESY IP, jednu pro každý řadič. Tyto pevné IP adresy lze použít pro přímý přístup k řadičům zařízení a jsou užitečné při instalaci aktualizací do zařízení, pro uvolnění paměti správně nebo při přístupu k řadičům za účelem řešení potíží.

Pevné řadiče IP můžete překonfigurovat pomocí okna nastavení DATA 0.

![Konfigurace síťového rozhraní – DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Pevné IP adresy pro řadič se používají pro obsluhu aktualizací zařízení a pro rekultivaci prostoru algoritmy (uvolňování paměti) pracovat správně. Pevné IP adresy proto musí být směrovatelné a schopné připojení k internetu.

### <a name="configure-data-1---data-5"></a>Konfigurace dat 1 – DATA 5

Pro síťová rozhraní DATA 1 - DATA 5 můžete nakonfigurovat všechna nastavení sítě, jak je znázorněno na následujícím snímku obrazovky:

![Konfigurace síťových rozhraní DATA 1 - DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Zaměnit nebo znovu přiřadit IP adresy

V současné době pokud je libovolnému síťovému rozhraní na řadiči přiřazena virtuální ip, která je používána (stejným zařízením nebo jiným zařízením v síti), přejde převzetí služeb při selhání. Pokud zaměníte nebo znovu přiřadíte virtuální ip adresy pro síťové rozhraní zařízení, musíte postupovat podle správného postupu, protože můžete vytvořit duplicitní situaci IP.

Proveďte následující kroky k prohození nebo opětovnému přiřazení virtuálních ip služeb pro libovolné síťové rozhraní:

#### <a name="to-reassign-ips"></a>Opětovné přiřazení IP adresy

1. Vymažte adresu IP pro obě rozhraní.
2. Po vymazání IP adres přiřaďte nové IP adresy příslušným rozhraním.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [nakonfigurovat mpio pro vaše zařízení StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Přečtěte si, jak [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

