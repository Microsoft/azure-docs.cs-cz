---
title: Poradce při potížích se spřažením návštěv
titleSuffix: Azure Application Gateway
description: Tento článek obsahuje informace o řešení problémů se spřažením relací v Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 9f14521c15c3497bed4ffbeba44cb5d78ee4df7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74047992"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Poradce při potížích se spřažením relací brány Azure Application Gateway

Zjistěte, jak diagnostikovat a řešit problémy se spřažením relací s Azure Application Gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled

Funkce spřažení relací na základě souborů cookie je užitečná v případě, že chcete zachovat uživatelskou relaci na stejném serveru. Pomocí souborů cookie spravovaných bránou umí služba Application Gateway směrovat následný provoz z uživatelské relace ke zpracování na stejný server. To je důležité v případech, kdy se stav jednotlivých uživatelských relací ukládá místně na serveru.

## <a name="possible-problem-causes"></a>Možné příčiny problému

K problému při udržování spřažení relací založených na souborech cookie může dojít z následujících hlavních důvodů:

- Nastavení Spřažení založené na souborech cookie není povoleno.
- Vaše aplikace nemůže zpracovat spřažení založenou na souborech cookie.
- Aplikace používá spřažení založené na souborech cookie, ale požadavky stále odrážejí mezi servery back-end

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Zkontrolujte, zda je povoleno nastavení Spřažení založené na souborech cookie

Někdy může dojít k problémům se spřažením relace, když zapomenete povolit nastavení "Spřažení založené na souborech cookie". Chcete-li zjistit, zda jste povolili nastavení "Spřažení na základě souborů cookie" na kartě Nastavení PROTOKOLU HTTP na webu Azure Portal, postupujte podle pokynů:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. V **levém navigačním** podokně klikněte na **Všechny prostředky**. Klikněte na název aplikační brány v okně Všechny prostředky. Pokud předplatné, které jste vybrali, již obsahuje několik prostředků, můžete zadat název brány aplikace do **pole Filtr podle názvu...** pro snadný přístup ke službě Application Gateway.

3. V části **NASTAVENÍ**vyberte kartu **Nastavení protokolu HTTP** .

   ![poradce při potížích-session-afinita-problémy-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Klikněte na **appGatewayBackendHttpSettings** na pravé straně a zkontrolujte, zda jste vybrali **Možnost Povoleno** pro spřažení založené na souborech cookie.

   ![poradce při potížích-session-afinita-problémy-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Můžete také zkontrolovat hodnotu "**CookieBasedAffinity**" je nastavena na *Povoleno*pod "**backendHttpSettingsCollection**" pomocí jedné z následujících metod:

- Spuštění [nastavení Get-AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) v PowerShellu
- Projděte si soubor JSON pomocí šablony Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Aplikace nemůže zpracovat spřažení založenou na souborech cookie.

#### <a name="cause"></a>Příčina

Aplikační brána může provádět spřažení na základě relace pouze pomocí souboru cookie.

#### <a name="workaround"></a>Alternativní řešení

Pokud aplikace nemůže zpracovat spřažení založené na souborech cookie, musíte použít externí nebo interní azure vyrovnávání zatížení nebo jiné řešení jiného jiného výrobce.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Aplikace používá spřažení založené na souborech cookie, ale požadavky stále odrážejí mezi servery back-end

#### <a name="symptom"></a>Příznak

Při přístupu k bráně aplikace pomocí adresy URL krátkého názvu v aplikaci Internet Explorer jste [http://website](http://website/) povolili nastavení spřažení založené na souborech cookie, například: , požadavek se stále odráží mezi servery back-end.

Chcete-li tento problém identifikovat, postupujte podle pokynů:

1. Vezměte trasování webového ladicího programu na "Klient", který se připojuje k aplikaci za aplikační bránou (v tomto příkladu používáme Šumař).
    **Tip** Pokud nevíte, jak používat Šumař, zaškrtněte možnost "**Chci sbírat síťový provoz a analyzovat jej pomocí webového ladicího programu**" v dolní části.

2. Zkontrolujte a analyzujte protokoly relací, abyste zjistili, zda soubory cookie poskytované klientem mají podrobnosti o ARRAffinity. Pokud nenajdete podrobnosti ARRAffinity, například "**ARRAffinity =** *ARRAffinityValue*" v sadě souborů cookie, to znamená, že klient neodpovídá se souborem cookie ARRA, který je poskytován aplikační bránou.
    Například:

    ![poradce při potížích-session-afinita-problémy-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![poradce při potížích-session-afinita-problémy-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

Aplikace pokračuje ve snaze nastavit soubor cookie na každý požadavek, dokud neobdrží odpověď.

#### <a name="cause"></a>Příčina

K tomuto problému dochází, protože internet explorer a jiné prohlížeče nemusí ukládat nebo používat soubor cookie s krátkým názvem URL.

#### <a name="resolution"></a>Řešení

Pokud chcete tento problém vyřešit, měli byste ke službě Application Gateway přistupovat pomocí plně kvalifikovaného názvu domény. Například použijte [http://website.com](https://website.com/) [http://appgw.website.com](http://appgw.website.com/) nebo .

## <a name="additional-logs-to-troubleshoot"></a>Další protokoly k řešení potíží

Můžete shromažďovat další protokoly a analyzovat je k řešení problémů souvisejících se spřažením relací založených na souborech cookie

### <a name="analyze-application-gateway-logs"></a>Analyzovat protokoly aplikační brány

Chcete-li shromažďovat protokoly brány aplikace, postupujte podle pokynů:

Povolit protokolování prostřednictvím webu Azure Portal

1. Na [webu Azure Portal](https://portal.azure.com/)vyhledejte svůj prostředek a klikněte na **diagnostické protokoly**.

   Pro aplikační bránu jsou k dispozici tři protokoly: Protokol přístupu, Protokol výkonu, Protokol brány firewall

2. Chcete-li začít shromažďovat data, klepněte na tlačítko **Zapnout diagnostiku**.

   ![poradce při potížích-session-afinita-problémy-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Okno **Nastavení diagnostiky** poskytuje nastavení diagnostických protokolů. V tomto příkladu Log Analytics ukládá protokoly. Klikněte v části **Log Analytics** na **konfigurovat** pracovní prostor. Diagnostické protokoly můžete ukládat také pomocí služby center událostí a účtu úložiště.

   ![poradce při potížích-session-afinita-problémy-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Potvrďte nastavení a klepněte na tlačítko **Uložit**.

   ![poradce při potížích-session-afinita-problémy-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Zobrazení a analýza protokolů přístupu k aplikační bráně

1. Na portálu Azure v zobrazení prostředků Aplikační brána vyberte **protokoly diagnostiky** v části **MONITOROVÁNÍ** .

   ![poradce při potížích-session-afinita-problémy-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Na pravé straně vyberte v rozevíracím seznamu v části **Kategorie protokolu položku** **ApplicationGatewayAccessLog**.  

   ![poradce při potížích-session-afinita-problémy-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. V seznamu Protokol přístupu k aplikační bráně klikněte na protokol, který chcete analyzovat a exportovat, a exportujte soubor JSON.

4. Převeďte soubor JSON, který jste exportovali v kroku 3, do souboru CSV a zobrazte ho v Excelu, Power BI nebo v jakémkoli jiném nástroji pro vizualizaci dat.

5. Zkontrolujte následující údaje:

- **ClientIP**– Toto je IP adresa klienta z připojujícího se klienta.
- **ClientPort** - Toto je zdrojový port z připojujícího se klienta pro požadavek.
- **RequestQuery** – Označuje cílový server, který je přijat požadavek.
- **Server-Routed**: Back-end pool instance, která je přijata požadavek.
- **X-AzureApplicationGateway-LOG-ID**: ID korelace použité pro požadavek. Lze jej použít k řešení problémů s provozem na serverech back-end. Příklad: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **STAV SERVERU:** Kód odpovědi HTTP, který aplikace gateway obdržela ze back-endu.

  ![poradce při potížích-session-afinita-problémy-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Pokud se zobrazí dvě položky pocházejí ze stejného ClientIP a klientského portu a jsou odesílány na stejný server back-end, znamená to, že aplikační brána byla správně nakonfigurována.

Pokud se zobrazí dvě položky pocházejí ze stejného ClientIP a klientského portu a jsou odesílány na různé back-end servery, to znamená, že požadavek se odráží mezi servery back-end, vyberte "**Aplikace používá spřažení založené na souborech cookie, ale požadavky stále odráží mezi back-end servery**" v dolní části k řešení potíží.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Použití webového ladicího programu k zachycení a analýze přenosů HTTP nebo HTTPS

Nástroje pro ladění webu, jako je Fiddler, vám mohou pomoci ladit webové aplikace zachycením síťového provozu mezi Internetem a testovacími počítači. Tyto nástroje umožňují kontrolovat příchozí a odchozí data při příjmu/odeslání prohlížeče. Fiddler, v tomto příkladu má možnost přehrání HTTP, která vám může pomoci vyřešit problémy na straně klienta s webovými aplikacemi, zejména pro ověřování druh problému.

Použijte webový ladicí program podle vašeho výběru. V této ukázce použijeme Fiddler k zachycení a analýze http nebo https přenosů, postupujte podle pokynů:

1. Stáhněte si nástroj <https://www.telerik.com/download/fiddler>Šumař na adrese .

    > [!NOTE]
    > Pokud je v zachytávajícím počítači nainstalována .NET 4, zvolte Fiddler4. V opačném případě zvolte Fiddler2.

2. Klepněte pravým tlačítkem myši na spustitelný soubor instalace a spusťte jej jako správce, který chcete nainstalovat.

    ![poradce při potížích-session-afinita-problémy-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Když otevřete Fiddler, měl by automaticky spustit zachycení provozu (všimněte si zachycení v levém dolním rohu). Stisknutím klávesy F12 spusťte nebo zastavíte snímání provozu.

    ![poradce při potížích-session-afinita-problémy-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. S největší pravděpodobností vás bude zajímat dešifrovaný provoz HTTPS a můžete povolit dešifrování https výběrem možnosti **nástroje** > **Fiddler Options**a zaškrtněte políčko " **Dešifrovat provoz HTTPS**".

    ![poradce při potížích-session-afinita-problémy-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Před reprodukcí problému můžete odstranit předchozí nesouvisející relace kliknutím na **X** (ikona) > **Odebrat vše** následujícím s ním: 

    ![poradce při potížích-session-afinita-problémy-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Po reprodukování problému uložte soubor k recenzi výběrem **možnosti Uložit** > **Save** > **všechny relace..**. 

    ![poradce při potížích-session-afinita-problémy-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Zkontrolujte a analyzujte protokoly relací, abyste zjistili, o jaký problém se jedná.

    Příklady:

- **Příklad A:** Zjistíte protokol relace, že požadavek je odeslán z klienta a přejde na veřejnou IP adresu aplikační brány, klepněte na tento protokol zobrazíte podrobnosti.  Na pravé straně data v dolním poli je to, co brána aplikace se vrací klientovi. Vyberte kartu "RAW" a určete, zda klient přijímá "**Set-Cookie: ARRAffinity =** *ARRAffinityValue*." Pokud není žádný soubor cookie, není nastavena spřažení relací nebo aplikační brána nepoužije soubor cookie zpět klientovi.

   > [!NOTE]
   > Tato hodnota ARRAffinity je cookie-id, který aplikační brána nastaví pro klienta, který má být odeslán na konkrétní server back-end.

   ![poradce při potížích-session-afinita-problémy-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Příklad B:** Další protokol relace následovaný předchozím je klient, který odpovídá zpět na aplikační bránu, která nastavila ARRAAFFINITY. Pokud se shoduje soubor cookie ARRAffinity, paket by měl být odeslán na stejný server back-end, který byl použit dříve. Zkontrolujte další několik řádků http komunikace a zjistěte, zda se mění soubor cookie ARRAffinity klienta.

   ![poradce při potížích-session-afinita-problémy-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Pro stejnou relaci komunikace by se soubor cookie neměl měnit. Zaškrtněte horní políčko na pravé straně, vyberte kartu "Soubory cookie", abyste zjistili, zda klient používá soubor cookie, a odešlete jej zpět do aplikační brány. Pokud tomu tak není, prohlížeč klienta není vedení a používání cookie pro konverzace. Někdy může klient lhát.

 

## <a name="next-steps"></a>Další kroky

Pokud předchozí kroky problém nevyřeší, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).
