---
title: Řešení potíží s spřažením relace Azure Application Gateway
description: Tento článek obsahuje informace o odstraňování potíží spřažením relace ve službě Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: d98834abaf925cdabd312869615a60f64049826d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808475"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Řešení potíží s spřažením relace Azure Application Gateway

Zjistěte, jak k diagnostikování a vyřešení spřažením relace pomocí Azure Application Gateway.

## <a name="overview"></a>Přehled

Funkce spřažení relací na základě souborů cookie je užitečná v případě, že chcete zachovat uživatelskou relaci na stejném serveru. Pomocí souborů cookie spravovaných bránou umí služba Application Gateway směrovat následný provoz z uživatelské relace ke zpracování na stejný server. To je důležité v případech, kdy se stav jednotlivých uživatelských relací ukládá místně na serveru.

## <a name="possible-problem-causes"></a>Způsobí, že možný problém

Problém při udržování spřažení relace na základě souborů cookie může dojít z následujících hlavních důvodů:

- Není povoleno nastavení "na základě souboru cookie spřažení"
- Aplikace nemůže zpracovat spřažení na základě souborů cookie
- Aplikace používá spřažení na základě souborů cookie ale požadavky stále skákání mezi back-end servery

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Zkontrolujte, zda je povoleno nastavení "na základě souboru Cookie spřažení"

Spřažením relace může někdy dojít k "Na základě souboru Cookie spřažení" nastavení povolte, pokud zapomenete. Pokud chcete zjistit, zda jste povolili "Na základě souboru Cookie spřažení" nastavení na kartě nastavení protokolu HTTP na webu Azure Portal, postupujte podle pokynů:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. V **levé navigační** podokně klikněte na tlačítko **všechny prostředky**. Klikněte na název brány aplikací v okně všechny prostředky. Pokud předplatné, které jste vybrali, již má několik prostředků, můžete zadat název brány aplikace **filtrovat podle názvu...** pro snadný přístup ke službě Application Gateway.

3. Vyberte **nastavení HTTP** kartu **nastavení**.

   ![řešení potíží s relace spřažení – problémy s-1](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-1.png)

4. Klikněte na tlačítko **appGatewayBackendHttpSettings** na pravé straně, chcete-li zkontrolovat, zda jste vybrali **povoleno** pro spřažení na základě souboru Cookie.

   ![řešení potíží s relace spřažení – problémy s-2](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-2.jpg)



Můžete také zkontrolovat hodnoty "**CookieBasedAffinity**" je nastavena na *povoleno*v části "**backendHttpSettingsCollection**" pomocí jedné z následujících metod:

- Run [Get-AzureRmApplicationGatewayBackendHttpSettings](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermapplicationgatewaybackendhttpsettings?view=azurermps-4.1.0) in PowerShell
- Prohlédněte si soubor JSON s použitím šablony Azure Resource Manageru

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Aplikace nemůže zpracovat spřažení na základě souborů cookie

#### <a name="cause"></a>Příčina

Aplikační brána lze provést pouze spřažení na základě relace pomocí souboru cookie.

#### <a name="workaround"></a>Alternativní řešení

Pokud aplikace nemůže zpracovat spřažení na základě souborů cookie, musíte použít externí nebo interní azure load balancer nebo jiné řešení třetí strany.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Aplikace používá spřažení na základě souborů cookie ale požadavky stále skákání mezi back-end servery

#### <a name="symptom"></a>Příznak

Při přístupu k Application Gateway pomocí adresy URL krátký název v aplikaci Internet Explorer, například jste povolili nastavení spřažení na základě souborů Cookie: [ http://website ](http://website/) , žádost je stále skákání mezi back-end servery.

Pokud chcete identifikovat problém, postupujte podle pokynů:

1. Využijte ladicí program webového trasování trasování v "Klientovi", který se připojuje k aplikace za bránou Gateway(We are using Fiddler in this example) aplikace.
    **Tip** Pokud si nejste jisti, jak použít aplikaci Fiddler, zaškrtněte možnost "**budu chtít shromažďovat síťový provoz a analyzujte je pomocí ladicího programu webového**" v dolní části.

2. Zkontrolovat a analyzovat protokoly relace, chcete-li zjistit, jestli mají soubory cookie, který klient poskytl ARRAffinity podrobnosti. Pokud nenajdete ARRAffinity podrobnosti, jako například "**ARRAffinity =** *ARRAffinityValue*" v rámci sady souborů cookie, která znamená, že klient není odpovědi s EŘADIT soubor cookie, který je poskytován Application Gateway.
    Příklad:

    ![řešení potíží s relace spřažení – problémy – 3](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-3.png)

        ![troubleshoot-session-affinity-issues-4](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-4.png)

Aplikace i nadále pokusu o nastavení souborů cookie s každým požadavkem, dokud nedosáhnete odpověď.

#### <a name="cause"></a>Příčina

K tomuto problému dochází, protože aplikace Internet Explorer a ostatní prohlížeče nemůže uložit nebo použít soubor cookie s adresou URL krátký název.

#### <a name="resolution"></a>Řešení

Chcete-li vyřešit tento problém, by měl přístup k službě Application Gateway pomocí úplného názvu domény. Například použít [ http://website.com ](http://website.com/) nebo [ http://appgw.website.com ](http://appgw.website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Další protokoly k řešení potíží

Můžete shromažďovat další protokoly a analyzovat, aby řešit problémy související relace na základě souboru cookie spřažení

### <a name="analyze-application-gateway-logs"></a>Analýza protokolů Application Gateway

Shromažďování protokolů Application Gateway, postupujte podle pokynů:

Povolit protokolování prostřednictvím webu Azure Portal

1. V [webu Azure portal](https://portal.azure.com/)vyhledejte váš prostředek a potom klikněte na tlačítko **diagnostické protokoly**.

   Pro službu Application Gateway jsou k dispozici tři protokoly: Přístup k protokolu v protokolu výkonu, brány Firewall protokolu

2. Spuštění sběru dat, klikněte na tlačítko **zapnout diagnostiku**.

   ![řešení potíží s relace spřažení – problémy – 5](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-5.png)

3. **Nastavení diagnostiky** okno obsahuje nastavení pro diagnostické protokoly. V tomto příkladu služba Log Analytics ukládá protokoly. Klikněte na tlačítko **konfigurovat** pod **Log Analytics** nastavit váš pracovní prostor. Diagnostické protokoly můžete ukládat také pomocí služby center událostí a účtu úložiště.

   ![řešení potíží s relace spřažení – problémy – 6](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-6.png)

4. Potvrďte nastavení a potom klikněte na tlačítko **Uložit**.

   ![řešení potíží s relace spřažení – problémy – 7](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Zobrazení a analýza protokolů Application Gateway přístup

1. Na webu Azure portal v části zobrazení zdrojů službě Application Gateway, vyberte **diagnostické protokoly** v **monitorování** oddílu.

   ![řešení potíží s relace spřažení – problémy-8](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-8.png)

2. Na pravé straně vyberte "**ApplicationGatewayAccessLog**" v rozevíracím seznamu v části **protokolu kategorií.**  

   ![řešení potíží s relace spřažení – problémy-9](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-9.png)

3. V seznamu přístup protokol aplikační brány klikněte na protokol, který chcete analyzovat a exportovat a exportovat soubor JSON.

4. Převést soubor JSON, který jste exportovali v kroku 3 do souboru CSV a zobrazit je v Excelu, Power BI nebo jakýkoli jiný nástroj na vizualizaci dat.

5. Zkontrolujte následující data:

- **Když**– Toto je IP adresa klienta z připojujícího se klienta.
- **ClientPort** – to je zdrojový port z připojujícího se klienta pro daný požadavek.
- **RequestQuery** – to znamená na cílový server obdržel žádost.
- **Server-Routed**: Instance back endového fondu přijetí požadavku.
- **X-AzureApplicationGateway-LOG-ID**: ID korelace použitou pro danou žádost. Slouží k řešení potíží s přenosy na back-end serverech. Příklad: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **STAV SERVERU**: Kód odpovědi HTTP, které služba Application Gateway přijaté z back-endu.

  ![řešení potíží s relace spřažení – problémy – 11.](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-11.png)

Pokud se zobrazí dvě položky přicházející ze stejné ClientIP a Port klienta a jsou odeslány do stejného back endového serveru, znamená to Aplikační brána správně nakonfigurovaná.

Pokud se zobrazí dvě položky přicházející ze stejné ClientIP a Port klienta a odešlou se na různé back endové servery, to znamená, že žádost je skákání mezi back-end serverů, vyberte možnost "**aplikace používá spřažení na základě souborů cookie ale požadavků stále skákání mezi back-end servery**"na konci článku Poradce při potížích.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Použijte ladicí program webového zaznamenávat a analyzovat traffics HTTP nebo HTTPS

Web ladicí nástroje, jako je Fiddler, vám může pomoci ladit webové aplikace pomocí zachycení síťového provozu mezi Internetem a testovací počítače. Tyto nástroje umožňují kontrolovat příchozí a odchozí data jako prohlížeči přijímá a odesílá je. Fiddler, v tomto příkladu má povolenou možnost přehrání HTTP, které vám můžou pomoct vyřešit problémy na straně klienta s webovými aplikacemi, zejména pro typ ověřování vydání.

Pomocí ladicího programu webového podle vašeho výběru. V této ukázce používáme Fiddler k zachycení a analýze traffics http nebo https, postupujte podle pokynů:

1. Stáhněte si nástroj Fiddler na <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Zvolte Fiddler4 pokud zachycující počítači nainstalováno rozhraní .NET 4. Jinak klikněte na tlačítko Fiddler2.

2. Klikněte pravým tlačítkem na spustitelný soubor nastavení a spustit jako správce k instalaci.

            ![troubleshoot-session-affinity-issues-12](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-12.png)

3. Když otevřete Fiddler, by měly automaticky spustit zachytávání provozu (Všimněte si, že zachytávání v levém dolním rohu). Stisknutím klávesy F12 spustit nebo zastavit zachytávání provozu.

        ![troubleshoot-session-affinity-issues-13](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-13.png)

4. Pravděpodobně vás bude zajímat dešifrovaný komunikaci přes protokol HTTPS a povolíte dešifrování HTTPS tak, že vyberete **nástroje** > **možnosti Fiddleru**a zaškrtněte políčko " **dešifrovat Přenosy HTTPS**".

        ![troubleshoot-session-affinity-issues-14](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-14.png)

5. Předchozí relace nesouvisejících před reprodukci problému kliknutím můžete odebrat **X** (ikona) > **odebrat všechny** jako postupujte snímku obrazovky: 

        ![troubleshoot-session-affinity-issues-15](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-15.png)

6. Po reprodukování problému uložit tak, že vyberete soubor ke kontrole **souboru** > **Uložit** > **všechny relace...** . 

        ![troubleshoot-session-affinity-issues-16](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-16.png)

7. Zkontrolovat a analyzovat protokoly relace k určení, co je problém.

    Příklady:

- **Příklad A:** Najít relaci protokolu, který žádost se poslala z klienta, a přejde na veřejnou IP adresu služby Application Gateway, klikněte na tomto protokolu zobrazíte podrobnosti.  Na pravé straně data v dolní části pole je Application Gateway vrácením klientovi. Vyberte kartu "RAW" a určit, zda klient přijímá "**Set-Cookie: ARRAffinity =** *ARRAffinityValue*. " Pokud neexistuje žádný soubor cookie, spřažení relace není nastavená nebo Application Gateway se nevztahují souboru cookie zpět do klienta.

   > [!NOTE]
   > Tato hodnota ARRAffinity je soubor cookie-id, nastavující Application Gateway pro klienta k odeslání do konkrétní back endového serveru.

    ![řešení potíží s relace spřažení – problémy-17.](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-17.png)

- **Příklad B:** Další relace protokolu a předchozí jeden je klient zpět ke službě Application Gateway, která byla nastavena ARRAAFFINITY reagovat. Pokud odpovídá id souboru cookie ARRAffinity, odešlou se ke stejnému back-end serveru, který se použil dříve paketu. Zkontrolujte další několik řádků komunikaci protokolem http, pokud chcete zobrazit, jestli se soubor cookie klienta ARRAffinity mění.

    ![řešení potíží s relace spřažení – problémy – 18.](.\media\how-to-troubleshoot-application-gateway-session-affinity-issues\troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Pro stejnou relaci komunikace by měl soubor cookie nebudou je moci měnit. Zaškrtnutím tohoto políčka nejvyšší na pravé straně, vyberte kartu soubory cookie."Pokud chcete zobrazit, zda je klienta pomocí souboru cookie a odesláním zpět ke službě Application Gateway. Pokud ne, není udržování a pomocí souboru cookie pro konverzací prohlížeče klienta. V některých případech může být od klienta.

 

## <a name="next-steps"></a>Další postup

Pokud předchozí kroky není problém vyřešit, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).