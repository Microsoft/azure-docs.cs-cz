---
title: Řešení potíží s spřažením relací
titleSuffix: Azure Application Gateway
description: Tento článek poskytuje informace o tom, jak řešit problémy s spřažením relací v Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 5acf4ac1ec75d5cec057e4b66e3c6cbd8a463271
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/16/2020
ms.locfileid: "84808018"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Řešení potíží s spřažením relací Azure Application Gateway

Naučte se diagnostikovat a řešit problémy s spřažením relací pomocí Azure Application Gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled

Funkce spřažení relací na základě souborů cookie je užitečná v případě, že chcete zachovat uživatelskou relaci na stejném serveru. Pomocí souborů cookie spravovaných bránou umí služba Application Gateway směrovat následný provoz z uživatelské relace ke zpracování na stejný server. To je důležité v případech, kdy se stav jednotlivých uživatelských relací ukládá místně na serveru.

## <a name="possible-problem-causes"></a>Možné příčiny problémů

Problém s údržbou spřažení relací na základě souborů cookie může nastat z následujících hlavních důvodů:

- Nastavení spřažení na základě souborů cookie se nepovoluje.
- Vaše aplikace nemůže zpracovat spřažení na základě souborů cookie.
- Aplikace používá spřažení na základě souborů cookie, ale požadavky se pořád skákající mezi back-end servery.

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Zkontroluje, jestli je povolené nastavení "spřažení na základě souborů cookie".

V některých případech se může stát, že k problémům spřažení relací dojde, když zapomenete povolit nastavení "spřažení na základě souborů cookie". Pokud chcete zjistit, jestli jste na kartě nastavení protokolu HTTP v Azure Portal povolili nastavení "spřažení na základě souborů cookie", postupujte podle pokynů:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. V **levém navigačním** podokně klikněte na **všechny prostředky**. V okně všechny prostředky klikněte na název aplikační brány. Pokud předplatné, které jste vybrali, již obsahuje několik prostředků, můžete zadat název služby Application Gateway do pole **filtrovat podle názvu...** pro snadný přístup ke službě Application Gateway.

3. V části **Nastavení**vyberte kartu **nastavení protokolu HTTP** .

   ![řešení potíží – spřažení relace – problémy-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Na pravé straně klikněte na **appGatewayBackendHttpSettings** a ověřte, jestli jste pro spřažení na základě souborů cookie vybrali **povolené** .

   ![řešení potíží – spřažení relace – problémy – 2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Můžete také ověřit, že hodnota "**CookieBasedAffinity**" je nastavena na hodnotu *povoleno*v části "**backendHttpSettingsCollection**" pomocí jedné z následujících metod:

- Spuštění rutiny [Get-AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) v PowerShellu
- Prohlédněte si soubor JSON pomocí šablony Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Aplikace nemůže zpracovat spřažení na základě souborů cookie.

#### <a name="cause"></a>Příčina

Služba Application Gateway může spřažení na základě relace provádět pouze pomocí souboru cookie.

#### <a name="workaround"></a>Alternativní řešení

Pokud aplikace nemůže zpracovat spřažení na základě souborů cookie, je nutné použít externí nebo interní nástroj pro vyrovnávání zatížení Azure nebo jiné řešení třetí strany.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Aplikace používá spřažení na základě souborů cookie, ale požadavky se pořád skákající mezi back-end servery.

#### <a name="symptom"></a>Příznak

Povolili jste nastavení spřažení na základě souborů cookie při přístupu k Application Gateway pomocí adresy URL krátkého názvu v aplikaci Internet Explorer, například: `http://website` , požadavek se stále skákající mezi back-end servery.

Chcete-li tento problém identifikovat, postupujte podle pokynů:

1. Proveďte trasování webového ladicího programu na "klient", který se připojuje k aplikaci za Application Gateway (v tomto příkladu používáme Fiddler).
    **Tip** Pokud si nejste jisti, jak používat Fiddler, podívejte se na možnost "**Chci shromažďovat síťový provoz a analyzovat ho pomocí webového ladicího programu**" v dolní části.

2. Zkontrolujte a analyzujte protokoly relací, abyste zjistili, jestli soubory cookie poskytované klientem mají ARRAffinity podrobnosti. Pokud v sadě souborů cookie nenajdete ARRAffinity podrobnosti, jako je například "**ARRAffinity =** *ARRAffinityValue*", znamená to, že klient neodpoví pomocí souboru cookie Arra, který je k dispozici Application Gateway.
    Příklad:

    ![řešení potíží – spřažení relace – problémy-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![řešení potíží – spřažení relace – problémy-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

Aplikace bude pokračovat v pokusu o nastavení souboru cookie v každé žádosti, dokud nebude odpovídat.

#### <a name="cause"></a>Příčina

K tomuto problému dochází, protože aplikace Internet Explorer a jiné prohlížeče nemusí ukládat nebo používat soubory cookie s krátkým názvem URL.

#### <a name="resolution"></a>Řešení

Pokud chcete tento problém vyřešit, měli byste ke službě Application Gateway přistupovat pomocí plně kvalifikovaného názvu domény. Například použijte [http://website.com](https://website.com/) nebo [http://appgw.website.com](http://website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Další protokoly pro řešení potíží

Můžete shromažďovat další protokoly a analyzovat je, abyste mohli řešit problémy související s přidružením relace na základě souborů cookie.

### <a name="analyze-application-gateway-logs"></a>Analyzovat protokoly Application Gateway

Pokud chcete shromáždit protokoly Application Gateway, postupujte podle pokynů:

Povolit protokolování prostřednictvím webu Azure Portal

1. V [Azure Portal](https://portal.azure.com/)vyhledejte prostředek a potom klikněte na **diagnostické protokoly**.

   Pro Application Gateway jsou k dispozici tři protokoly: protokol přístupu, protokol výkonu, protokol brány firewall.

2. Pokud chcete začít shromažďovat data, klikněte na **zapnout diagnostiku**.

   ![řešení potíží – spřažení relace – problémy-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Okno **nastavení diagnostiky** poskytuje nastavení pro diagnostické protokoly. V tomto příkladu Log Analytics ukládá protokoly. Kliknutím na **Konfigurovat** v části **Log Analytics** nastavte pracovní prostor. Diagnostické protokoly můžete ukládat také pomocí služby center událostí a účtu úložiště.

   ![řešení potíží – spřažení relace – problémy – 6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Potvrďte nastavení a klikněte na **Uložit**.

   ![řešení potíží – spřažení relace – problémy – 7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Zobrazení a analýza protokolů Application Gateway Access

1. V Azure Portal v zobrazení prostředků Application Gateway v části **monitorování** vyberte **protokoly diagnostiky** .

   ![řešení potíží – spřažení relace – problémy-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Na pravé straně v rozevíracím seznamu **Kategorie protokolů** vyberte "**ApplicationGatewayAccessLog**".  

   ![řešení potíží – spřažení relace – problémy – 9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. V seznamu protokol přístupu Application Gateway klikněte na protokol, který chcete analyzovat a exportovat, a pak exportujte soubor JSON.

4. Převeďte soubor JSON, který jste exportovali v kroku 3, do souboru CSV a zobrazte ho v Excelu, Power BI nebo jakémkoli jiném nástroji pro vizualizaci dat.

5. Podívejte se na tato data:

- **IP adresa klienta**– jedná se o IP adresu klienta z připojujícího se klienta.
- **ClientPort** – jedná se o zdrojový port od připojujícího se klienta k žádosti.
- **RequestQuery** – Určuje cílový server, který požadavek přijal.
- **Směrováno serverem**: instance fondu back-end, kterou požadavek přijal.
- **X-AzureApplicationGateway-log-ID**: ID korelace použité pro požadavek. Dá se použít k řešení problémů s přenosem na back-endové servery. Například: X-AzureApplicationGateway-CACHE-PŘÍSTUPŮ = 0&SERVER-Route = 10.0.2.4.

  - **Stav serveru**: kód odpovědi HTTP, který Application Gateway přijatý od back-endu.

  ![řešení potíží – spřažení relace – problémy-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Pokud se zobrazí dvě položky pocházející ze stejného IP adresa klienta a portu klienta a odesílají se na stejný back-end Server, znamená to, že Application Gateway správně nakonfigurované.

Pokud se zobrazí dvě položky ze stejného IP adresa klienta a klientského portu a odesílají se na různé back-endové servery, znamená to, že požadavek je skákající mezi back-end servery, vyberte "**aplikace používá spřažení na základě souborů cookie, ale požadavky pořád skákající mezi back-end servery**" v dolní části, abyste je mohli vyřešit.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Použití webového ladicího programu k zachycení a analýze přenosů HTTP nebo HTTPS

Nástroje pro ladění webu, jako je Fiddler, vám můžou přispět k ladění webových aplikací zachytáváním síťového provozu mezi Internetem a testovacími počítači. Tyto nástroje umožňují kontrolu příchozích a odchozích dat, když je prohlížeč obdrží nebo pošle. Fiddler v tomto příkladu má možnost opětovného přehrání HTTP, která vám může pomoct při odstraňování problémů s webovými aplikacemi na straně klienta, zejména při ověřování typu problém.

Použijte webový ladicí program podle vašeho výběru. V této ukázce použijeme Fiddler k zachycení a analýze přenosů http nebo https, postupujte podle pokynů:

1. Stáhněte si nástroj Fiddler na adrese <https://www.telerik.com/download/fiddler> .

    > [!NOTE]
    > Vyberte Fiddler4, pokud je v zachytávání počítač nainstalovaný .NET 4. V opačném případě vyberte možnost Fiddler2.

2. Klikněte pravým tlačítkem na spustitelný soubor instalace a spusťte jako správce a nainstalujte.

    ![řešení potíží – spřažení relace – problémy – 12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Při otevření Fiddler by se mělo automaticky spustit zachycení provozu (Všimněte si zachycení v levém dolním rohu). Stisknutím klávesy F12 spustíte nebo zastavíte zachytávání provozu.

    ![řešení potíží – spřažení relace – problémy – 13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Pravděpodobně budete zajímat dešifrovaný provoz https a můžete povolit dešifrování pomocí protokolu HTTPS výběrem **nástrojů**  >  **Fiddler Options**a zaškrtnutím políčka " **dešifrování přenosů https**".

    ![řešení potíží – spřažení relace – problémy – 14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Předchozí nesouvisející relace můžete odebrat před tím, že kliknete na tlačítko **X** (ikona) > **Odebrat vše** jako snímek obrazovky s následujícím snímkem: 

    ![řešení potíží – spřažení relace – problémy-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Po reprodukování problému uložte soubor k revizi tak, že vyberete **soubor**  >  **Uložit**  >  **všechny relace..**. 

    ![řešení potíží – spřažení relace – problémy – 16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Zkontrolujte a analyzujte protokoly relací, abyste zjistili, co je problém.

    Příklady:

- **Příklad:** Najdete protokol relace, který požadavek odesílá z klienta, a odkazuje na veřejnou IP adresu Application Gateway, kliknutím na tento protokol zobrazíte podrobnosti.  Na pravé straně jsou data v dolním poli, která Application Gateway vrací klientovi. Vyberte kartu "RAW" a určete, zda klient přijímá "**Set-cookie: ARRAffinity =** *ARRAffinityValue*". Pokud není soubor cookie, spřažení relace není nastavené nebo Application Gateway nepoužívá soubor cookie zpátky na klienta.

   > [!NOTE]
   > Tato hodnota ARRAffinity je ID souboru cookie, které Application Gateway sada pro klienta k odeslání na konkrétní back-end Server.

   ![řešení potíží – spřažení relace – problémy – 17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Příklad B:** Další protokol relace následovaný předchozí příponou klienta odpoví zpět na Application Gateway, která nastavila ARRAAFFINITY. Pokud se shoduje s ID souboru cookie ARRAffinity, paket by měl být odeslán na stejný back-end Server, který byl použit dříve. Pokud chcete zjistit, jestli se mění soubor cookie klienta ARRAffinity, podívejte se na několik dalších řádků komunikace http.

   ![řešení potíží – spřažení relace – problémy – 18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Pro stejnou relaci komunikace by se soubor cookie neměl měnit. Zaškrtněte horní políčko na pravé straně, vyberte kartu soubory cookie a ověřte, zda klient používá soubor cookie a odesílá jej zpět do Application Gateway. V takovém případě prohlížeč klienta neudržuje a nepoužívá soubor cookie pro konverzace. V některých případech může klient narazit.

 

## <a name="next-steps"></a>Další kroky

Pokud předchozí kroky problém nevyřeší, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).
