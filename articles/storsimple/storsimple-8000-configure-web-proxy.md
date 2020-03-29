---
title: Nastavení webového proxy serveru pro zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Naučte se, jak pomocí prostředí Windows PowerShell pro StorSimple nakonfigurovat nastavení webového proxy serveru pro vaše zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 956cf45eb9e246f2e1f917f2bf487ac14deba90e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65204254"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurace webového proxy serveru pro zařízení StorSimple

## <a name="overview"></a>Přehled

Tento kurz popisuje, jak pomocí prostředí Windows PowerShell pro StorSimple konfigurovat a zobrazit nastavení webového proxy serveru pro vaše zařízení StorSimple. Nastavení webového proxy serveru používá zařízení StorSimple při komunikaci s cloudem. Webový proxy server se používá k přidání další vrstvy zabezpečení, obsahu filtru, mezipaměti pro usnadnění požadavků na šířku pásma nebo dokonce pomoci s analýzou.

Pokyny v tomto kurzu platí pouze pro fyziomská zařízení řady StorSimple 8000. Konfigurace webového proxy serveru není podporována na zařízení StorSimple Cloud Appliance (8010 a 8020).

Webový proxy server je _volitelná_ konfigurace pro vaše zařízení StorSimple. Webový proxy server můžete konfigurovat pouze prostřednictvím prostředí Windows PowerShell pro službu StorSimple. Konfigurace je dvoustupňový proces následujícím způsobem:

1. Nejprve nakonfigurujete nastavení webového proxy serveru pomocí průvodce instalací nebo prostředí Windows PowerShell pro rutiny StorSimple.
2. Potom povolit nakonfigurované nastavení webového proxy serveru prostřednictvím prostředí Windows PowerShell pro rutiny StorSimple.

Po dokončení konfigurace webového proxy serveru můžete zobrazit nakonfigurované nastavení webového proxy serveru ve službě Microsoft Azure StorSimple Device Manager a prostředí Windows PowerShell for StorSimple.

Po přečtení tohoto výukového programu, budete moci:

* Nakonfigurujte webový proxy server pomocí průvodce nastavením a rutin.
* Povolte webový proxy server pomocí rutin.
* Zobrazení nastavení webového proxy serveru na webu Azure Portal.
* Poradce při potížích při konfiguraci webového proxy serveru.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurace webového proxy serveru prostřednictvím prostředí Windows PowerShell pro StorSimple

Ke konfiguraci nastavení webového proxy serveru slouží některá z následujících možností:

* Průvodce instalací, který vás provede kroky konfigurace.
* Rutiny v prostředí Windows PowerShell pro StorSimple.

Každá z těchto metod je popsána v následujících částech.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurace webového proxy serveru pomocí průvodce nastavením

Pomocí průvodce nastavením vás provedete kroky konfigurace webového proxy serveru. Chcete-li nakonfigurovat webový proxy server v zařízení, proveďte následující kroky.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Konfigurace webového proxy serveru pomocí průvodce nastavením

1. V nabídce sériové konzole zvolte možnost 1, **Přihlaste se s úplným přístupem** a zadejte **heslo správce zařízení**. Chcete-li spustit relaci průvodce instalací, zadejte následující příkaz:
   
    `Invoke-HcsSetupWizard`
2. Pokud je to poprvé, co jste použili průvodce nastavením pro registraci zařízení, je třeba nakonfigurovat všechna požadovaná nastavení sítě, dokud nedosáhnete konfigurace webového proxy serveru. Pokud je vaše zařízení již zaregistrováno, přijměte všechna nakonfigurovaná nastavení sítě, dokud nedosáhnete konfigurace webového proxy serveru. Po zobrazení výzvy ke konfiguraci nastavení webového proxy serveru zadejte v průvodci instalací příkaz **Ano**.
3. Pro **adresu URL webového proxy serveru**zadejte adresu IP nebo plně kvalifikovaný název domény (FQDN) webového proxy serveru a číslo portu TCP, které má vaše zařízení používat při komunikaci s cloudem. Použijte tento formát:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Ve výchozím nastavení je zadán port TCP číslo 8080.
4. Zvolte typ ověřování jako **NTLM**, **Basic**nebo **None**. Základní je nejméně zabezpečené ověřování pro konfiguraci serveru proxy. NT LAN Manager (NTLM) je vysoce zabezpečený a složitý ověřovací protokol, který k ověření uživatele používá třícestný systém zasílání zpráv (někdy čtyři, pokud je vyžadována další integrita). Výchozí ověřování je NTLM. Další informace naleznete [v tématu Basic](https://hc.apache.org/httpclient-3.x/authentication.html) a [NTLM authentication](https://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **Ve službě StorSimple Device Manager nefungují grafy monitorování zařízení, pokud je v konfiguraci proxy serveru pro zařízení povoleno ověřování Basic nebo NTLM. Aby monitorovací grafy fungovaly, je třeba zajistit, aby ověřování bylo nastaveno na MOŽNOST ŽÁDNÉ.**
  
5. Pokud jste ověření povolili, zadej **uživatelské jméno webového proxy serveru** a heslo **webového proxy serveru**. Musíte také potvrdit heslo.
   
    ![Konfigurace webového proxy serveru na zařízení StorSimple1](./media/storsimple-configure-web-proxy/IC751830.png)

Pokud zařízení registrujete poprvé, pokračujte v registraci. Pokud již bylo zařízení zaregistrováno, průvodce program ukončí. Nakonfigurovaná nastavení se uloží.

Webový proxy server je nyní povolen. Krok [Povolit webový proxy server](#enable-web-proxy) můžete přeskočit a přejít přímo na [Zobrazit nastavení webového proxy serveru na webu Azure Portal](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurace webového proxy serveru prostřednictvím prostředí Windows PowerShell pro rutiny StorSimple

Alternativní způsob konfigurace nastavení webového proxy serveru je prostřednictvím prostředí Windows PowerShell pro rutiny StorSimple. Chcete-li nakonfigurovat webový proxy server, proveďte následující kroky.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Konfigurace webového proxy serveru pomocí rutin
1. V nabídce sériové konzole zvolte možnost 1, **Přihlaste se s úplným přístupem**. Po zobrazení výzvy zadejte **heslo správce zařízení**. Výchozí heslo `Password1`je .
2. Na příkazovém řádku zadejte:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Po zobrazení výzvy zadejte a potvrďte heslo.
   
    ![Konfigurace webového proxy serveru na zařízení StorSimple3](./media/storsimple-configure-web-proxy/IC751831.png)

Webový proxy server je nyní nakonfigurován a je třeba jej povolit.

## <a name="enable-web-proxy"></a>Povolení webového proxy serveru

Webový proxy server je ve výchozím nastavení zakázán. Po konfiguraci nastavení webového proxy serveru na zařízení StorSimple povolte nastavení webového proxy serveru pomocí prostředí Windows PowerShell pro StorSimple.

> [!NOTE]
> **Tento krok není nutný, pokud jste ke konfiguraci webového proxy serveru použili průvodce nastavením. Webový proxy server je po relaci průvodce instalací automaticky povolen ve výchozím nastavení.**


Chcete-li v zařízení povolit webový proxy server, proveďte v prostředí Windows PowerShell pro Službu StorSimple následující kroky:

#### <a name="to-enable-web-proxy"></a>Povolení webového proxy serveru
1. V nabídce sériové konzole zvolte možnost 1, **Přihlaste se s úplným přístupem**. Po zobrazení výzvy zadejte **heslo správce zařízení**. Výchozí heslo `Password1`je .
2. Na příkazovém řádku zadejte:
   
    `Enable-HcsWebProxy`
   
    Nyní jste povolili konfiguraci webového proxy serveru na zařízení StorSimple.
   
    ![Konfigurace webového proxy serveru na zařízení StorSimple4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Zobrazení nastavení webového proxy serveru na webu Azure Portal

Nastavení webového proxy serveru je konfigurováno prostřednictvím rozhraní prostředí Windows PowerShell a nelze je změnit z portálu. Tato nakonfigurovaná nastavení však můžete zobrazit na portálu. Chcete-li zobrazit webový proxy server, proveďte následující kroky.

#### <a name="to-view-web-proxy-settings"></a>Zobrazení nastavení webového proxy serveru
1. Přejděte na **službu Správce zařízení StorSimple > zařízení**. Vyberte zařízení a klepněte na něj a přejděte na **nastavení zařízení > síť**.

    ![Klikněte na Síť.](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. V okně **Nastavení sítě** klepněte na dlaždici **webového proxy** serveru.

    ![Klikněte na webový proxy server.](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. V okně **webového proxy** serveru zkontrolujte nakonfigurované nastavení webového proxy serveru na zařízení StorSimple.
   
    ![Zobrazení nastavení webového proxy serveru](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Chyby během konfigurace webového proxy serveru

Pokud je nastavení webového proxy serveru nakonfigurováno nesprávně, zobrazí se uživateli v prostředí Windows PowerShell pro StorSimple chybové zprávy. Následující tabulka vysvětluje některé z těchto chybových zpráv, jejich pravděpodobné příčiny a doporučené akce.

| Sériové číslo | Kód chyby HRESULT | Možná hlavní příčina | Doporučená akce |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Příkaz je spuštěn z pasivního řadiče a není schopen komunikovat s aktivním řadičem. |Spusťte příkaz na aktivním řadiči. Chcete-li spustit příkaz z pasivního řadiče, musíte opravit připojení z pasivního na aktivní řadič. Pokud je toto připojení přerušeno, je nutné zapojit podporu společnosti Microsoft. |
| 2. |0x800710dd - Identifikátor operace není platný. |Nastavení proxy serveru nejsou podporovány na StorSimple Cloud Appliance. |Nastavení proxy serveru nejsou podporovány na StorSimple Cloud Appliance. Ty lze nakonfigurovat pouze na fyzickém zařízení StorSimple. |
| 3. |0x80070057 - Neplatný parametr |Jeden z parametrů poskytnutých pro nastavení proxy serveru není platný. |Identifikátor URI není k dispozici ve správném formátu. Použijte následující formát:`http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - RPC server není k dispozici |Hlavní příčinou je jedna z následujících příčin:</br></br>Cluster není nahoře. </br></br>Služba Datapath není spuštěna.</br></br>Příkaz je spuštěn z pasivního řadiče a není schopen komunikovat s aktivním řadičem. |Zapojte podporu Microsoftu, abyste zajistili, že cluster je v provozu a je spuštěna služba datapath.</br></br>Spusťte příkaz z aktivního řadiče. Pokud chcete spustit příkaz z pasivního řadiče, musíte zajistit, aby pasivní řadič mohl komunikovat s aktivním řadičem. Pokud je toto připojení přerušeno, je nutné zapojit podporu společnosti Microsoft. |
| 5. |0x800706be - volání vzdáleného volání se nezdařilo. |Cluster nese. |Zapojte podporu Microsoftu, abyste zajistili, že cluster je nahoře. |
| 6. |0x8007138f - Prostředek clusteru nebyl nalezen. |Prostředek clusteru služby platformy nebyl nalezen. K tomu může dojít, když instalace nebyla správná. |V zařízení může být nutné provést obnovení továrního nastavení. Možná budete muset vytvořit prostředek platformy. O dalších krocích se poraďte s podporou Microsoftu. |
| 7. |0x8007138c - Prostředek clusteru není online |Prostředky clusteru platformy nebo datové cesty nejsou online. |Obraťte se na podporu společnosti Microsoft a otekvěte, zda jsou datové cesty a prostředky služby platformy online. |

> [!NOTE]
> * Výše uvedený seznam chybových zpráv není vyčerpávající.
> * Chyby související s nastavením webového proxy serveru se nezobrazí na webu Azure Portal ve službě Správce zařízení StorSimple. Pokud se po dokončení konfigurace objeví problém s webovým serverem proxy, stav zařízení se na klasickém portálu změní na **Offline.|**

## <a name="next-steps"></a>Další kroky
* Pokud při nasazování zařízení nebo konfiguraci nastavení webového proxy serveru narazíte na potíže [s nasazením zařízení StorSimple](storsimple-troubleshoot-deployment.md).
* Chcete-li se dozvědět, jak používat službu StorSimple Device Manager, přejděte [ke správě zařízení StorSimple pomocí služby StorSimple Device Manager](storsimple-8000-manager-service-administration.md).

