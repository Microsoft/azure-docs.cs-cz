---
title: Nastavení webového proxy serveru pro zařízení StorSimple řady 8000 | Dokumentace Microsoftu
description: Zjistěte, jak pomocí prostředí Windows PowerShell pro StorSimple můžete nakonfigurovat nastavení webového proxy serveru pro zařízení StorSimple.
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
ms.openlocfilehash: be5719d2c383c838ef70c6862c1055c3374e05e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362443"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurace webového proxy serveru pro zařízení StorSimple

## <a name="overview"></a>Přehled

Tento kurz popisuje, jak pomocí prostředí Windows PowerShell pro StorSimple ke konfiguraci a zobrazení nastavení webového proxy serveru pro zařízení StorSimple. Nastavení webového proxy serveru využívají zařízení StorSimple při komunikaci s cloudem. Webový proxy server se používá k přidání další vrstvu zabezpečení, filtrování obsahu mezipaměti, aby se požadavky na šířku pásma snadné nebo dokonce pomoc s analýzou.

Pokyny v tomto kurzu platí pouze pro fyzická zařízení řady StorSimple 8000. Cloudové zařízení StorSimple (8010 a 8020) nepodporuje konfigurace webového proxy serveru.

Webový proxy server je _volitelné_ konfigurace pro zařízení StorSimple. Můžete nakonfigurovat webový proxy server jenom přes Windows PowerShell pro StorSimple. Konfigurace je dvoustupňový proces následujícím způsobem:

1. Nejprve nakonfigurujte nastavení webového proxy serveru pomocí Průvodce instalací nebo prostředí Windows PowerShell pro StorSimple rutiny.
2. Pak povolte nastavení nakonfigurované webového proxy serveru pomocí Windows Powershellu pro StorSimple rutiny.

Po dokončení konfigurace webového proxy serveru můžete zobrazit nastavení nakonfigurované webového proxy serveru ve službě Microsoft Azure StorSimple Device Manager a prostředí Windows PowerShell pro StorSimple.

Po přečtení tohoto kurzu, budete moct:

* Konfigurace webového proxy serveru pomocí Průvodce instalací a rutin.
* Povolte webový proxy server pomocí rutin.
* Zobrazení nastavení webového proxy serveru na webu Azure Portal.
* Řešení chyb během konfigurace webového proxy serveru.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurace webového proxy serveru pomocí Windows Powershellu pro StorSimple

Ke konfiguraci nastavení webového proxy serveru použijte jednu z následujících:

* Průvodce instalací a provede vás provede kroky konfigurace.
* Rutiny v prostředí Windows PowerShell pro StorSimple.

Každá z těchto metod jsou popsány v následujících částech.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurace webového proxy serveru pomocí Průvodce instalací

Pomocí Průvodce instalací a provede vás provede kroky pro konfigurace webového proxy serveru. Proveďte následující postup pro konfiguraci webového proxy serveru na vašem zařízení.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Konfigurace webového proxy serveru pomocí Průvodce instalací

1. V nabídce konzoly sériového portu, vyberte možnost 1, **přihlášení pomocí úplný přístup** a poskytnout **hesla správce zařízení**. Zadejte následující příkaz pro spuštění relace Průvodce instalací:
   
    `Invoke-HcsSetupWizard`
2. Pokud to je poprvé, kdy používáte Průvodce instalací pro registraci zařízení, budete muset konfigurovat všechna potřebná nastavení sítě, dokud se nedostanete konfigurace webového proxy serveru. Pokud vaše zařízení je už zaregistrovaný, přijměte všechny nakonfigurovaná nastavení sítě, dokud nedosáhnete konfigurace webového proxy serveru. V Průvodci instalací po zobrazení výzvy ke konfiguraci nastavení webového proxy serveru, zadejte **Ano**.
3. Pro **adresy URL webového proxy serveru**, zadejte IP adresu nebo název plně kvalifikované domény (FQDN) webového proxy serveru a číslo portu TCP, které chcete použít při komunikaci se službami pro cloudové zařízení. Použijte následující formát:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Ve výchozím nastavení je zadán TCP port číslo 8080.
4. Zvolte typ ověřování jako **NTLM**, **základní**, nebo **žádný**. Základní je nejméně bezpečná ověřování pro konfiguraci proxy serveru. NT LAN Manager (NTLM) je vysoce zabezpečené a komplexní ověřovací protokol, který používá třícestné systému zasílání zpráv (někdy čtyři Pokud je potřeba další integrity) k ověření uživatele. Výchozí ověřování je NTLM. Další informace najdete v tématu [základní](http://hc.apache.org/httpclient-3.x/authentication.html) a [ověřování protokolem NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **Ve službě Správce zařízení StorSimple grafy monitorování zařízení, nebudou fungovat Basic nebo v konfiguraci proxy serveru pro toto zařízení je povolené ověřování NTLM. Monitorování grafy pro práci budete muset zajistit, že ověřování je nastavena na hodnotu NONE.**
  
5. Pokud jste povolili ověřování, zadejte **uživatelské jméno Proxy webové** a **heslo webového proxy serveru**. Je také potřeba Potvrdit heslo.
   
    ![Konfigurace webového proxy serveru na StorSimple zařízení1](./media/storsimple-configure-web-proxy/IC751830.png)

Pokud registrujete zařízení poprvé, pokračujte registraci. Pokud vaše zařízení se už zaregistrovali, průvodce se ukončí. Nakonfigurovaná nastavení se ukládají.

Webový proxy server je nyní zapnuta. Můžete přeskočit [povolit webový proxy server](#enable-web-proxy) krok a přejděte přímo na [zobrazení nastavení webového proxy serveru na webu Azure Portal](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurace webového proxy serveru pomocí Windows Powershellu pro StorSimple rutiny

Alternativní způsob konfigurace nastavení webového proxy serveru je přes Windows PowerShell pro StorSimple rutiny. Proveďte následující postup pro konfiguraci webového proxy serveru.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Konfigurace webového proxy serveru pomocí rutiny
1. V nabídce konzoly sériového portu, vyberte možnost 1, **přihlášení pomocí úplný přístup**. Po zobrazení výzvy zadejte **hesla správce zařízení**. Výchozí heslo je `Password1`.
2. Na příkazovém řádku zadejte:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Zadejte a potvrďte heslo po zobrazení výzvy.
   
    ![Konfigurace webového proxy serveru na StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Webový proxy server je teď nakonfigurovaná a je potřeba povolit.

## <a name="enable-web-proxy"></a>Povolit webový proxy server

Webový proxy server je ve výchozím nastavení zakázána. Po nakonfigurování nastavení webového proxy serveru na zařízení StorSimple pomocí prostředí Windows PowerShell pro StorSimple můžete povolit nastavení webového proxy serveru.

> [!NOTE]
> **Tento krok není povinný, pokud jste použili Průvodce instalací do konfigurace webového proxy serveru. Webový proxy server je automaticky povolené ve výchozím nastavení po relaci Průvodce instalací.**


V prostředí Windows PowerShell pro StorSimple umožňující webového proxy serveru na vašem zařízení postupujte takto:

#### <a name="to-enable-web-proxy"></a>Povolit webový proxy server
1. V nabídce konzoly sériového portu, vyberte možnost 1, **přihlášení pomocí úplný přístup**. Po zobrazení výzvy zadejte **hesla správce zařízení**. Výchozí heslo je `Password1`.
2. Na příkazovém řádku zadejte:
   
    `Enable-HcsWebProxy`
   
    Nyní jste povolili konfigurace webového proxy serveru na vašem zařízení StorSimple.
   
    ![Konfigurace webového proxy serveru na StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Zobrazení nastavení webového proxy serveru na webu Azure Portal

Nastavení webového proxy serveru jsou konfigurovány pomocí rozhraní Windows PowerShell a nejde změnit z v rámci portálu. Ale zobrazí se nastavení nakonfigurované na portálu. Proveďte následující kroky, chcete-li zobrazit webový proxy server.

#### <a name="to-view-web-proxy-settings"></a>Chcete-li zobrazit nastavení webového proxy serveru
1. Přejděte do **služby Správce zařízení StorSimple > zařízení**. Vyberte a klikněte na zařízení a potom přejděte k **nastavení zařízení > sítě**.

    ![Klikněte na síť](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. V **nastavení sítě** okna, klikněte na tlačítko **webového proxy serveru** dlaždici.

    ![Klikněte na webový proxy server](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. V **webového proxy serveru** okno, projděte si nastavení nakonfigurované webového proxy serveru na vašem zařízení StorSimple.
   
    ![Zobrazení nastavení webového proxy serveru](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Chyby během konfigurace webového proxy serveru

Pokud nastavení webového proxy serveru jsou nesprávně nakonfigurované, se zobrazí chybové zprávy pro uživatele v prostředí Windows PowerShell pro StorSimple. Následující tabulka popisuje některé z těchto chybových zpráv, jejich možných příčin a doporučené akce.

| Sériové č. | Chyba HRESULT kódu | Možnou hlavní příčinou | Doporučená akce |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Příkaz spustí z pasivní kontroler a není schopen komunikovat s aktivním řadiči. |Spusťte příkaz na aktivním řadiči. Ke spuštění příkazu z pasivního kontroleru, je nutné opravit připojení z pasivního na aktivním řadiči. Microsoft Support musí zapojit, pokud tyto možnosti připojení bylo přerušeno. |
| 2. |0x800710dd - identifikátor operace není platná |Nastavení proxy serveru nejsou podporovány v řešení StorSimple Cloud Appliance. |Nastavení proxy serveru nejsou podporovány v řešení StorSimple Cloud Appliance. Dají se nakonfigurovat jenom na fyzickém zařízení StorSimple. |
| 3. |0x80070057 – neplatný parametr |Jeden z parametrů stanovených nastavení proxy serveru není platný. |Není k dispozici identifikátor URI ve správném formátu. Použijte následující formát: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - server RPC není k dispozici |Hlavní příčinou je jeden z následujících akcí:</br></br>Clusteru není zapnutý. </br></br>DataPath služba není spuštěná.</br></br>Nastavení se příkaz spustí z pasivní kontroler a není schopen komunikovat s aktivním řadiči. |Zapojení Microsoft Support zajistit, že cluster běží a je spuštěná služba datapath.</br></br>Spusťte příkaz z aktivního kontroleru. Pokud chcete spustit příkaz z pasivní kontroler, ujistěte se, že pasivní kontroler může komunikovat s aktivním řadiči. Microsoft Support musí zapojit, pokud tyto možnosti připojení bylo přerušeno. |
| 5. |0X800706be - RPC volání se nezdařilo. |Clusteru je mimo provoz. |Zapojení Microsoft Support zajistit, že cluster běží. |
| 6. |0x8007138f - nebyl nalezen prostředek clusteru |Prostředek clusteru služby platformy nebyl nalezen. To může nastat při instalaci nebyla správná. |Budete muset provést na vašem zařízení obnovit tovární nastavení. Budete muset vytvořit prostředek platformy. O dalších krocích se poraďte s podporou Microsoftu. |
| 7. |0x8007138c - prostředek clusteru není online |Platforma nebo datapath prostředků clusteru nejsou online. |Obraťte se na Microsoft Support pomáhá zajistit, že prostředek služby datapath a platforma jsou online. |

> [!NOTE]
> * Nad seznamem chybových zpráv není vyčerpávající.
> * Chyby související nastavení webového proxy serveru se nezobrazí na portálu Azure ve službě Správce zařízení StorSimple. Pokud se vyskytl problém s proxy serverem, webovou po dokončení konfigurace, stav zařízení se změní na **Offline** na portálu classic. |

## <a name="next-steps"></a>Další kroky
* Pokud zaznamenáte problémy při nasazení zařízení nebo konfigurace nastavení webového proxy serveru, podívejte se na [odstranění problémů s nasazením zařízení StorSimple](storsimple-troubleshoot-deployment.md).
* Další informace o použití služby Správce zařízení StorSimple, přejděte na [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

