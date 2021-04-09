---
title: Nastavení webového proxy serveru pro zařízení řady StorSimple 8000 | Microsoft Docs
description: Naučte se, jak pomocí Windows PowerShell pro StorSimple nakonfigurovat nastavení webového proxy serveru pro zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 48671e7558ea1bd613d33372c96fa3c563407e81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017215"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurace webového proxy serveru pro zařízení StorSimple

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak pomocí Windows PowerShell pro StorSimple nakonfigurovat a zobrazit nastavení webového proxy serveru pro zařízení StorSimple. Nastavení webového proxy serveru používá zařízení StorSimple při komunikaci s cloudem. Web proxy server se používá k přidání další vrstvy zabezpečení, filtrování obsahu, mezipaměti pro usnadnění požadavků na šířku pásma nebo dokonce i k analýze.

Pokyny v tomto kurzu se vztahují jenom na fyzická zařízení řady StorSimple 8000. Konfigurace webového proxy serveru není v StorSimple Cloud Appliance podporována (8010 a 8020).

Webový proxy server je _volitelná_ konfigurace pro zařízení StorSimple. Webový proxy server můžete nakonfigurovat pouze prostřednictvím Windows PowerShell pro StorSimple. Konfigurace je proces se dvěma kroky, jak je znázorněno níže:

1. Nakonfigurujete nastavení webového proxy serveru pomocí Průvodce instalací nebo rutin Windows PowerShell pro StorSimple.
2. Pak můžete pomocí rutin Windows PowerShell pro StorSimple povolit nakonfigurovaná nastavení webového proxy serveru.

Po dokončení konfigurace webového proxy serveru můžete zobrazit nakonfigurovaná nastavení webového proxy serveru jak ve službě Microsoft Azure StorSimple Správce zařízení, tak i v Windows PowerShell pro StorSimple.

Po přečtení tohoto kurzu budete moct:

* Nakonfigurujte webový proxy server pomocí Průvodce instalací a rutin.
* Povolte webový proxy server pomocí rutin.
* Zobrazit nastavení webového proxy serveru v Azure Portal.
* Řešení chyb během konfigurace webového proxy serveru.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurace webového proxy serveru přes Windows PowerShell pro StorSimple

Pro konfiguraci nastavení webového proxy serveru použijte jednu z následujících možností:

* Průvodce instalací, který vás provede kroky konfigurace.
* Rutiny v Windows PowerShell pro StorSimple.

Každá z těchto metod je popsána v následujících částech.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurace webového proxy serveru prostřednictvím Průvodce instalací

Pomocí Průvodce instalací vás provede kroky pro konfiguraci webového proxy serveru. Provedením následujících kroků nakonfigurujte webový proxy server na svém zařízení.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Konfigurace webového proxy serveru prostřednictvím Průvodce instalací

1. V nabídce Konzola sériového portu vyberte možnost 1, **Přihlaste se s úplným přístupem** a zadejte **heslo správce zařízení**. Zadáním následujícího příkazu spusťte relaci Průvodce instalací:
   
    `Invoke-HcsSetupWizard`
2. Pokud jste použili Průvodce instalací pro registraci zařízení poprvé, budete muset nakonfigurovat všechna požadovaná nastavení sítě, dokud se nedostanete ke konfiguraci webového proxy serveru. Pokud je zařízení už zaregistrované, přijměte všechna nakonfigurovaná nastavení sítě, dokud nedosáhnete konfigurace webového proxy serveru. Po zobrazení výzvy ke konfiguraci nastavení webového proxy serveru v Průvodci instalací zadejte **Ano**.
3. Pro **adresu URL webového proxy serveru** zadejte IP adresu nebo plně kvalifikovaný název domény (FQDN) vašeho webového proxy server a číslo portu TCP, které má vaše zařízení používat při komunikaci s cloudem. Použijte tento formát:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Ve výchozím nastavení je zadané číslo portu TCP 8080.
4. Jako typ ověřování vyberte **NTLM**, **Basic** nebo None ( **žádný**). Základní je nejméně zabezpečené ověřování pro konfiguraci proxy server. NT LAN Manager (NTLM) je vysoce zabezpečený a složitý ověřovací protokol, který používá třícestný systém zasílání zpráv (někdy čtyři, pokud je potřeba další integrita) k ověření uživatele. Výchozí ověřování je NTLM. Další informace najdete v tématu ověřování [Basic](https://hc.apache.org/httpclient-3.x/authentication.html) a [NTLM](https://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **V StorSimple Správce zařízení služba nefungují grafy monitorování zařízení v případě, že je v konfiguraci proxy server pro zařízení povolené základní ověřování nebo ověřování NTLM. Aby grafy monitorování fungovaly, je nutné zajistit, aby bylo ověřování nastaveno na možnost žádné.**
  
5. Pokud jste povolili ověřování, zadejte **uživatelské jméno webového proxy serveru** a **heslo webového proxy serveru**. Také je nutné potvrdit heslo.
   
    ![Konfigurace webového proxy serveru v StorSimple Zařízení1](./media/storsimple-configure-web-proxy/IC751830.png)

Pokud zaregistrujete zařízení poprvé, pokračujte v registraci. Pokud je vaše zařízení už zaregistrované, průvodce se ukončí. Nakonfigurovaná nastavení se uloží.

Webový proxy server je teď povolený. Můžete přeskočit krok [Povolit webový proxy server](#enable-web-proxy) a přejít přímo k [zobrazení nastavení webového proxy serveru v Azure Portal](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurace webového proxy serveru prostřednictvím rutin Windows PowerShell pro StorSimple

Alternativním způsobem konfigurace nastavení webového proxy serveru je pomocí rutin Windows PowerShell pro StorSimple. Pro konfiguraci webového proxy serveru proveďte následující kroky.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Konfigurace webového proxy prostřednictvím rutin
1. V nabídce Konzola sériového portu klikněte na možnost 1, **Přihlaste se s úplným přístupem**. Po zobrazení výzvy zadejte **heslo správce zařízení**. Výchozí heslo je `Password1` .
2. Na příkazovém řádku zadejte:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Po zobrazení výzvy zadejte a potvrďte heslo.
   
    ![Konfigurace webového proxy serveru v StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Webový proxy server je teď nakonfigurovaný a musí být povolený.

## <a name="enable-web-proxy"></a>Povolit webový proxy server

Webový proxy server je ve výchozím nastavení zakázán. Po nakonfigurování nastavení webového proxy serveru na zařízení StorSimple použijte Windows PowerShell pro StorSimple k povolení nastavení webového proxy serveru.

> [!NOTE]
> **Tento krok není nutný, pokud jste použili Průvodce instalací ke konfiguraci webového proxy serveru. Po relaci Průvodce instalací je webový proxy server automaticky povolen ve výchozím nastavení.**


Pokud chcete na svém zařízení povolit webový proxy server, proveďte následující kroky v Windows PowerShell pro StorSimple:

#### <a name="to-enable-web-proxy"></a>Povolení webového proxy serveru
1. V nabídce Konzola sériového portu klikněte na možnost 1, **Přihlaste se s úplným přístupem**. Po zobrazení výzvy zadejte **heslo správce zařízení**. Výchozí heslo je `Password1` .
2. Na příkazovém řádku zadejte:
   
    `Enable-HcsWebProxy`
   
    Nyní jste povolili konfiguraci webového proxy serveru na zařízení StorSimple.
   
    ![Konfigurace webového proxy serveru v StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Zobrazit nastavení webového proxy serveru v Azure Portal

Nastavení webového proxy serveru se konfiguruje přes rozhraní Windows PowerShell a nedá se změnit v rámci portálu. Tato nakonfigurovaná nastavení můžete ale zobrazit na portálu. Chcete-li zobrazit webový proxy server, proveďte následující kroky.

#### <a name="to-view-web-proxy-settings"></a>Zobrazení nastavení webového proxy serveru
1. Přejděte na **StorSimple Správce zařízení služby > zařízení**. Vyberte zařízení a klikněte na něj a potom přejděte na **nastavení zařízení > síť**.

    ![Kliknout na síť](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. V okně **nastavení sítě** klikněte na dlaždici **webový proxy server** .

    ![Kliknout na webový proxy server](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. V okně **webový proxy server** Zkontrolujte nakonfigurované nastavení webového proxy serveru na zařízení StorSimple.
   
    ![Zobrazit nastavení webového proxy serveru](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Chyby během konfigurace webového proxy serveru

Pokud je nastavení webového proxy serveru nesprávně nakonfigurované, zobrazí se uživatelům v Windows PowerShell pro StorSimple chybové zprávy. Následující tabulka popisuje některé z těchto chybových zpráv, jejich pravděpodobné příčiny a doporučené akce.

| Sériové číslo. | Kód chyby HRESULT | Možná hlavní příčina | Doporučená akce |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Příkaz se spouští z pasivního kontroleru a není schopen komunikovat s aktivním řadičem. |Spusťte příkaz na aktivním řadiči. Chcete-li spustit příkaz z pasivního kontroleru, je nutné opravit připojení z pasivního řadiče na aktivní. Pokud je toto připojení přerušeno, je třeba zahájit podpora Microsoftu. |
| 2. |0x800710dd – identifikátor operace není platný. |Nastavení proxy serveru není na StorSimple Cloud Appliance podporováno. |Nastavení proxy serveru není na StorSimple Cloud Appliance podporováno. Tato nastavení se dají nakonfigurovat jenom na fyzickém zařízení StorSimple. |
| 3. |0x80070057 – neplatný parametr |Jeden z parametrů poskytnutých pro nastavení proxy serveru není platný. |Identifikátor URI není uveden ve správném formátu. Použijte následující formát: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba – Server RPC není k dispozici. |Hlavní příčinou je jedna z následujících:</br></br>Cluster není zapnutý. </br></br>Služba DataPath není spuštěna.</br></br>Příkaz se spouští z pasivního kontroleru a nemůže komunikovat s aktivním řadičem. |Zapojte se podpora Microsoftu a ujistěte se, že je cluster zapnutý a že je spuštěná služba DataPath.</br></br>Spusťte příkaz z aktivního řadiče. Pokud chcete spustit příkaz z pasivního kontroleru, je nutné zajistit, aby pasivní kontrolér mohl komunikovat s aktivním řadičem. Pokud je toto připojení přerušeno, je třeba zahájit podpora Microsoftu. |
| 5. |0X800706be – volání RPC selhalo. |Cluster je mimo provoz. |Zapojte se podpora Microsoftu a zajistěte, aby byl cluster zapnutý. |
| 6. |0x8007138f – prostředek clusteru se nenašel. |Prostředek clusteru služby platformy se nenašel. K tomu může dojít v případě, že instalace nebyla správná. |Možná budete muset na svém zařízení provést obnovení továrního nastavení. Možná budete muset vytvořit prostředek platformy. O dalších krocích se poraďte s podporou Microsoftu. |
| 7. |0x8007138c – prostředek clusteru není online. |Prostředky clusteru platformy nebo DataPath nejsou online. |Obraťte se na podpora Microsoftu, které vám pomohou zajistit, že je prostředek DataPath a Platform Service online. |

> [!NOTE]
> * Výše uvedený seznam chybových zpráv není vyčerpávající.
> * Chyby související s nastavením webového proxy serveru se nebudou zobrazovat v Azure Portal ve službě StorSimple Správce zařízení. Pokud dojde k potížím s webovým proxy serverem po dokončení konfigurace, stav zařízení se změní na **offline** na portálu Classic. |

## <a name="next-steps"></a>Další kroky
* Pokud dojde k problémům při nasazování zařízení nebo konfiguraci nastavení webového proxy serveru, přečtěte si téma [řešení potíží s nasazením zařízení StorSimple](./storsimple-8000-troubleshoot-deployment.md).
* Pokud se chcete dozvědět, jak používat službu StorSimple Správce zařízení, přečtěte si [použití služby StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).