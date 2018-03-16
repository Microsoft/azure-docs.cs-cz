---
title: "Postup použití řízení přístupu (Java) | Microsoft Docs"
description: "Zjistěte, jak vyvíjet a použití řízení přístupu s Java v Azure."
services: active-directory
documentationcenter: java
author: rmcmurray
manager: mtillman
editor: 
ms.assetid: 247dfd59-0221-4193-97ec-4f3ebe01d3c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: b555ef40fae8156d2957643697d6450ef22b215a
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Jak ověřovat uživatele webové službě Řízení přístupu Azure pomocí prostředí Eclipse
Tento průvodce vám ukáže, jak používat řízení přístupu Azure Service (ACS) v rámci sady nástrojů Azure pro Eclipse. Další informace o služby ACS, najdete v článku [další kroky](#next_steps) části.

> [!NOTE]
> Ovládací prvek filtru Azure přístup služeb je náhled technologie komunity. Jako předběžné verze softwaru není oficiálně společností Microsoft.
> 
> 

## <a name="what-is-acs"></a>Novinky služby ACS
Většina vývojářů nejsou odborníky identity a obecně nechcete zatěžovat vývoj mechanismy ověřování a autorizace na čas pro své aplikace a služby. Služby ACS je služba Azure, která poskytuje snadný způsob ověřování uživatelů, kteří potřebují přístup k vaší webové aplikace a služby, aniž by museli Multi-Factor komplexní ověřování logika do kódu.

Následující funkce jsou k dispozici v rámci služby ACS:

* Integrace s Windows Identity Foundation (WIF).
* Podpora zprostředkovatelů identity oblíbených webových (IP) včetně Windows Live ID, Google, Yahoo! a Facebook.
* Podpora pro Active Directory Federation Services (AD FS) 2.0.
* Open Data Protocol (OData) – na základě služba správy, které zajišťují programový přístup k nastavení služby ACS.
* Portál správy, který umožňuje přístup pro správu k nastavení služby ACS.

Další informace o ACS najdete v tématu [2.0 služby Řízení přístupu][Access Control Service 2.0].

## <a name="concepts"></a>Koncepty
Azure služby ACS je založen na objekty zabezpečení na základě deklarace identity – jednotný přístup k vytvoření mechanismy ověřování pro aplikace spuštěné místně nebo v cloudu. Na základě deklarace identity umožňuje společný pro aplikace a služby se získat informace, které potřebují o identitě uživatele uvnitř jejich organizace v jiných organizacích a na Internetu.

K dokončení úkolů v této příručce, byste měli vědět následující koncepty:

**Klient** – v kontextu tohoto postupu průvodce, je to prohlížeč, který se pokouší získat přístup k vaší webové aplikaci.

**Aplikace předávající stranu** – RP aplikace je webu nebo službu, která outsources ověřování pro jednu externí autoritu. V žargonu identity říkáme, RP důvěřuje této autoritě. Tato příručka vysvětluje, jak nakonfigurovat svoji aplikaci do vztahu důvěryhodnosti služby ACS.

**Token** -token je kolekce dat zabezpečení, která se obvykle objeví po úspěšném ověření uživatele. Obsahuje sadu *deklarace identity*, atributy ověřeného uživatele. Deklarace identity může představovat uživatelské jméno, identifikátor pro roli uživatele patří do, stáří uživatele a tak dále. Token je obvykle digitálně podepsané, což znamená, ho můžete vždy použít jako zdroj zpět do vystavitele a její obsah nemůže být úmyslně poškozena. Uživatel získá přístup k aplikaci RP prezentací platný token vydán autoritou, která důvěřuje RP aplikace.

**Zprostředkovatel identity (IP)** -IP je autoritu, která ověřuje identity uživatelů a vydává tokeny zabezpečení. Skutečné práci při vystavování tokenů se implementuje, když speciální služba s názvem tokenu služby zabezpečení (STS). Typické příklady IP adres patří Windows Live ID, Facebook, obchodní uživatele úložiště (třeba služby Active Directory) a tak dále.
Pokud služby ACS je nakonfigurován tak, aby důvěřoval IP adresy, bude systém přijmout a ověřit tokeny vydané podle této IP. Služba ACS může důvěřovat více IP adres najednou, což znamená, že pokud vaše aplikace důvěřuje služby ACS, můžete okamžitě nabízet vaší aplikace pro všechny ověřené uživatele z všechny IP adresy, vztahy důvěryhodnosti služby ACS vaším jménem.

**Federační zprostředkovatel (FP)** -IP adresy mají přímý znalosti o uživatele a ověřit jejich pomocí svých přihlašovacích údajů a vydat deklarace identity o vědí o nich. Federační zprostředkovatel (FP) je jiný druh autority: místo přímo ověřování uživatelů, funguje jako zprostředkovatel a zprostředkovatelé ověřování mezi jeden RP a jedním nebo více IP adres. IP adresy a FPs vystavovat tokeny zabezpečení, proto používají obě tokenu služby zabezpečení (STS). Služby ACS je jeden FP.

**Modul pravidel ACS** -logiku použitá k transformaci příchozí tokeny od důvěryhodné IP adresy na tokeny, měl by být využívány službou RP je právně upraveny v formu pravidla transformace jednoduchých deklarací identity. Služba ACS funkce stroj pravidel, která má na starosti použití libovolnou logiku transformace jste zadali pro vaše RP.

**Namespace ACS** – obor názvů je nejvyšší úrovně oddílu služby ACS, který používáte pro uspořádání nastavení. Obor názvů obsahuje seznam IP adres důvěřujete, RP aplikací, které má sloužit, pravidla, které předpokládáte, pravidlo modul pro zpracování příchozí tokeny se a tak dále. Obor názvů zpřístupní různé koncové body, které se použijí tak, že aplikace a vývojářům získat služby ACS, aby fungoval.

Následující obrázek ukazuje, jak funguje ověřovací služby ACS s webovou aplikací:

![Vývojový diagram služby ACS][acs_flow]

1. Klient (v tomto případě prohlížeče) stránka žádosti z RP.
2. Vzhledem k tomu, že ještě požadavek není ověřen, RP přesměruje uživatele na oprávnění, která důvěřuje, což je služby ACS. Služby ACS zobrazí uživatele s volba IP adresy, které bylo zadáno pro tento RP. Uživatel vybere odpovídající IP.
3. Klient přejde na stránku ověřování IP adresy a vyzývá uživatele k přihlášení.
4. Po ověření klienta (například identity, které jsou zadané přihlašovací údaje) IP vydá token zabezpečení.
5. Po vydání tokenu zabezpečení, IP přesměruje klienta do služby ACS a klient odešle tokenu zabezpečení vydaného IP služby ACS.
6. Služba ACS ověří tokenu zabezpečení vydaného IP adresy, vstupy identitu deklarace identity ve tento token do stroj pravidel služby ACS, vypočítá výstup deklarace identity a vydá nový token zabezpečení, která obsahuje tyto deklarace výstupu.
7. Služba ACS přesměruje klienta na RP. Klient odešle nový token zabezpečení vydané službou ACS pro RP. RP ověří podpis tokenu zabezpečení vydaného služby ACS, ověří deklarací identity ve tento token a vrátí stránku, která původně požádal.

## <a name="prerequisites"></a>Požadavky
K dokončení úkolů v této příručce, budete potřebovat následující:

* Java Developer Kit (JDK), v 1.6 nebo novější.
* Integrované vývojové prostředí Eclipse pro vývojáře v jazyce Java EE, džínovinu nebo novější. To si můžete stáhnout z <http://www.eclipse.org/downloads/>. 
* Distribuce založené na jazyce Java webový server nebo aplikačního serveru, jako je například Apache Tomcat, GlassFish, aplikační Server JBoss nebo Jetty.
* předplatné Azure, který můžete získat z <http://www.microsoft.com/windowsazure/offers/>.
* Verze sady nástrojů Azure pro prostředí Eclipse. dubna 2014 nebo vyšší. Další informace najdete v tématu [instalaci sady nástrojů Azure pro Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Certifikát X.509 používat s vaší aplikací. Je nutné tento certifikát veřejného certifikátu (.cer) a Personal Information Exchange (. Formát PFX). (Možnosti pro vytvoření tohoto certifikátu bude popsané později v tomto kurzu).
* Znalost Azure výpočetní emulátor a nasazení techniky popsané v [vytvoření aplikace Hello World služby Azure v prostředí Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Vytvoření služby ACS Namespace
Chcete-li začít používat služby Řízení přístupu (ACS) v Azure, musíte vytvořit na obor názvů služby ACS. Obor názvů poskytuje jedinečný obor pro adresování prostředků služby ACS z v rámci vaší aplikace.

1. Přihlaste se [portál pro správu Azure][Azure Management Portal].
2. Klikněte na tlačítko **služby Active Directory**. 
3. Chcete-li vytvořit nový obor názvů řízení přístupu, klikněte na tlačítko **nový**, klikněte na tlačítko **App Services**, klikněte na tlačítko **řízení přístupu**a potom klikněte na **rychle vytvořit**. 
4. Zadejte název pro obor názvů. Azure ověřuje, zda je název jedinečný.
5. Vyberte oblast, ve kterém se používá obor názvů. Nejlepšího výkonu dosáhnete použijte oblast, ve kterém jsou nasazení aplikace.
6. Pokud máte více než jedno předplatné, vyberte předplatné, se kterou chcete použít pro obor názvů služby ACS.
7. Klikněte na možnost **Vytvořit**.

Azure vytvoří a aktivuje obor názvů. Počkejte, dokud je stav Nový obor názvů **Active** než budete pokračovat. 

## <a name="add-identity-providers"></a>Přidat zprostředkovatele identity
V této úloze přidejte IP adresy používat s vaší aplikací RP pro ověřování. Pro demonstrační účely tato úloha ukazuje, jak přidat Windows Live jako IP adresy, ale můžete použít některý z IP adresy uvedené v portálu pro správu služby ACS.

1. V [portálu pro správu Azure][Azure Management Portal], klikněte na tlačítko **služby Active Directory**, vyberte na obor názvů řízení přístupu a pak klikněte na tlačítko **spravovat**. Otevře se na portálu pro správu služby ACS.
2. V levém navigačním podokně portálu pro správu služby ACS, klikněte na tlačítko **zprostředkovatelů Identity**.
3. Windows Live ID ve výchozím nastavení zapnutá a nelze ji odstranit. Pro účely tohoto kurzu se používá pouze Windows Live ID. Tato obrazovka se ale, kde můžete přidat další IP adresy, kliknutím **přidat** tlačítko.

Windows Live ID je teď povolené jako IP adresy pro obor názvů služby ACS. Dále určit webové aplikace Java (který se má vytvořit později) jako RP.

## <a name="add-a-relying-party-application"></a>Přidání aplikace předávající strany
V této úloze nakonfigurujete ACS rozpoznat jako platný aplikace RP webové aplikace Java.

1. Na portálu pro správu služby ACS, klikněte na tlačítko **aplikace předávající strany**.
2. Na **aplikace předávající strany** klikněte na tlačítko **přidat**.
3. Na **přidat aplikaci předávající strany** proveďte následující:
   
   1. V **název**, zadejte název RP. Pro účely tohoto kurzu, zadejte **webové aplikace Azure**.
   2. V **režimu**, vyberte **zadejte nastavení ručně**.
   3. V **sféry**, zadejte identifikátor URI, na které se vztahuje tokenu zabezpečení vydaného služby ACS. Pro tuto úlohu, zadejte **adrese http://localhost: 8080 /**.
      ![Předávající strany sféru pro použití v emulátoru služby výpočty][relying_party_realm_emulator]
   4. V **vrátí adresu URL,** zadejte adresu URL, na kterou ACS vrátí token zabezpečení. Pro tuto úlohu, zadejte **http://localhost:8080/MyACSHelloWorld/index.jsp**
      ![předávající strany návratovou adresu URL pro použití v emulátoru služby výpočty][relying_party_return_url_emulator]
   5. Přijměte výchozí hodnoty v ostatních polích.
4. Klikněte na **Uložit**.

Nyní jste úspěšně nakonfigurovali webové aplikace v jazyce Java při spuštění v emulátor výpočtů v Azure (na adrese http://localhost: 8080 /) jako RP v oboru názvů služby ACS. Dále vytvořte pravidla, která ke zpracování deklarace pro RP se používá služby ACS.

## <a name="create-rules"></a>Vytvoření pravidel
V této úloze definujete pravidel, která určují, jak deklarace identity jsou předávány z IP adres vaší RP. Pro účely tohoto průvodce můžeme jednoduše nakonfiguruje ACS kopírovat vstupní typy a hodnoty přímo v token výstupu bez jejich změně nebo filtrování.

1. Na hlavní stránce portálu pro správu služby ACS, klikněte na tlačítko **pravidla skupiny**.
2. Na **skupiny pravidel** klikněte na tlačítko **výchozí skupiny pravidel pro webové aplikace Azure**.
3. Na **upravit skupinu pravidel** klikněte na tlačítko **generování**.
4. Na **generováním pravidel: výchozí skupiny pravidel pro webové aplikace Azure** , zkontrolujte Windows Live ID je zaškrtnuta možnost a pak klikněte na tlačítko **generování**.    
5. Na **upravit skupinu pravidel** klikněte na tlačítko **Uložit**.

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Nahrání certifikátu do vašeho oboru názvů služby ACS
V této úloze nahrajete. Certifikát PFX, který se použije k podepisování žádostí o token vytvořený obor názvů služby ACS.

1. Na hlavní stránce portálu pro správu služby ACS, klikněte na tlačítko **certifikáty a klíče**.
2. Na **certifikáty a klíče** klikněte na tlačítko **přidat** výše **podepisování**.
3. Na **přidat Token podpisový certifikát nebo klíče** stránky:
   1. V **používá pro** klikněte na tlačítko **aplikaci předávající strany** a vyberte **webové aplikace Azure** (která dříve nastavená jako název aplikace předávající strany).
   2. V **typ** vyberte **certifikát X.509**.
   3. V **certifikát** části, klikněte na tlačítko Procházet a přejděte na soubor certifikátu X.509, který chcete použít. Bude jím. Soubor PFX. Vyberte soubor, klikněte na tlačítko **otevřete**a pak zadejte heslo certifikátu v **heslo** textové pole. Upozorňujeme, že pro účely testování, může použít samoobslužných-signed-certifikát. Chcete-li vytvořit certifikát podepsaný svým držitelem, použijte **nový** tlačítka na **ACS filtru knihovny** dialogové okno (popsané dál), nebo použijte **encutil.exe** nástroj z [projektu webu] [ project website] z Starter Kit Azure pro jazyk Java.
   4. Ujistěte se, že **zkontrolujte primární** je zaškrtnuté. Vaše **přidat Token podpisový certifikát nebo klíče** stránka by měla vypadat podobně jako následující.
       ![Přidat podpisový certifikát tokenu][add_token_signing_cert]
   5. Klikněte na tlačítko **Uložit** nastavení uložte a zavřete **přidat Token podpisový certifikát nebo klíče** stránky.

Dále zkontrolujte informace na stránce integraci aplikace a zkopírujte identifikátor URI, které budete potřebovat ke konfiguraci webové aplikace Java pomocí služby ACS.

## <a name="review-the-application-integration-page"></a>Zkontrolujte stránku integraci aplikací
Můžete najít všechny informace a kód nutné ke konfiguraci webové aplikace Java (RP aplikace) pro práci se službou ACS na integraci aplikací stránce portálu pro správu služby ACS. Tyto informace budete potřebovat při konfiguraci webové aplikace Java federovaného ověřování.

1. Na portálu pro správu služby ACS, klikněte na tlačítko **integraci aplikací**.  
2. V **integraci aplikací** klikněte na tlačítko **přihlašovací stránky**.
3. V **přihlašovací stránky integrace** klikněte na tlačítko **webové aplikace Azure**.

V **přihlašovací stránky integrace: webové aplikace Azure** adresy URL uvedené v stránky **možnost 1: odkaz na stránku přihlášení hostované služby ACS** se použije na webové aplikace v jazyce Java. Tuto hodnotu budete potřebovat při přidání knihovny filtru služeb řízení přístupu Azure pro aplikace v jazyce Java.

## <a name="create-a-java-web-application"></a>Vytvoření webové aplikace Java
1. V prostředí Eclipse, klikněte v nabídce **soubor**, klikněte na tlačítko **nový**a potom klikněte na **Dynamic Web Project**. (Pokud nevidíte **Dynamic Web Project** uvedené jako dostupné projekt po kliknutí na **soubor**, **nový**, postupujte takto: klikněte na tlačítko **soubor**, klikněte na tlačítko **nový**, klikněte na tlačítko **projektu**, rozbalte položku **webové**, klikněte na tlačítko **Dynamic Web Project**a klikněte na tlačítko  **Další**.) Pro účely tohoto kurzu, název projektu **MyACSHelloWorld**. (Ujistěte se, použijte tento název, váš soubor WAR s názvem MyACSHelloWorld očekávat následné kroky v tomto kurzu). Na obrazovce se zobrazí podobná této:
   
    ![Vytvoření projektu Hello, World pro exampple služby ACS][create_acs_hello_world]
   
    Klikněte na **Dokončit**.
2. V rámci zobrazení Eclipse na prohlížeči projektu rozbalte **MyACSHelloWorld**. Klikněte pravým tlačítkem na **WebContent**, pak na **New** (Nový) a nakonec na **JSP File** (Soubor JSP).
3. V **nový soubor JSP** dialogové okno, název souboru **index.jsp**. Nadřazený adresář ponechte na jako MyACSHelloWorld nebo WebContent, jak je znázorněno v následující:
   
    ![Přidá soubor JSP například služby ACS][add_jsp_file_acs]
   
    Klikněte na **Další**.
4. V **vybrat šablonu JSP** vyberte **nový soubor JSP (html)** a klikněte na tlačítko **Dokončit**.
5. Když se soubor index.jsp otevře v prostředí Eclipse, přidejte text k zobrazení **ACS Vítáme vás!** do existujícího elementu `<body>`. Aktualizovaný `<body>` obsah se mají zobrazit jako následující:
   
        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
   
    Uložte index.jsp.

## <a name="add-the-acs-filter-library-to-your-application"></a>Přidání filtru ACS knihovny do vaší aplikace
1. V prostředí Eclipse v prohlížeči projektu klikněte pravým tlačítkem na **MyACSHelloWorld**, klikněte na tlačítko **cesta sestavení**a potom klikněte na **konfigurovat cestu sestavení**.
2. V **cesta sestavení Java** dialogové okno, klikněte **knihovny** karta.
3. Klikněte na tlačítko **přidání knihovny**.
4. Klikněte na tlačítko **Azure přístup k řízení služby filtrovat (podle otevřete technická MS)** a pak klikněte na **Další**. **Filtru služeb řízení přístupu Azure** se zobrazí dialogové okno.  ( **Umístění** pole může mít jinou cestu, v závislosti na tom, kam jste nainstalovali Eclipse, a číslo verze může být liší v závislosti na aktualizace softwaru.)
   
    ![Přidání filtru ACS knihovny][add_acs_filter_lib]
5. Pomocí prohlížeče otevřít k **přihlašovací stránky integrace** stránky portálu pro správu, Kopírovat adresu URL uvedené v **možnost 1: odkaz na stránku přihlášení hostované služby ACS** pole a vložte ji do **služby ACS Koncový bod ověřování** pole Eclipse dialogového okna.
6. Pomocí prohlížeče otevřít k **upravit aplikaci předávající strany** stránky portálu pro správu, Kopírovat adresu URL uvedené v **sféry** pole a vložte ji do **předávající strany sféry**pole Eclipse dialogového okna.
7. V rámci **zabezpečení** části dialogového okna Eclipse, pokud chcete použít stávající certifikát, klikněte na tlačítko **Procházet**, přejděte k certifikátu, kterou chcete použít, vyberte ho a klikněte na tlačítko **otevřete**. Nebo, pokud chcete vytvořit nový certifikát, klikněte na tlačítko **nový** zobrazíte **nový certifikát** dialogové okno, zadejte heslo, název souboru .cer a název souboru .pfx pro nový certifikát.
8. Zkontrolujte **vložení certifikát do souboru WAR**. Vložení certifikát tímto způsobem ji obsahuje ve vašem nasazení aniž by bylo potřeba ručně přidat jako součást. (Pokud místo toho je třeba uložit vašeho certifikátu externě ze souboru WAR, můžete přidat certifikát jako součást role a zrušte zaškrtnutí políčka **vložení certifikát do souboru WAR**.)
9. [Nepovinné] Zachovat **připojení vyžadují HTTPS** zaškrtnutí. Pokud nastavíte tuto možnost, budete potřebovat pro přístup k aplikaci pomocí protokolu HTTPS. Pokud nechcete, aby tak, aby vyžadovala připojení prostřednictvím protokolu HTTPS, zrušte tuto možnost.
10. Pro nasazení emulátoru služby výpočty vaše **Azure ACS filtru** nastavení bude vypadat podobně jako následující.
    
    ![ACS filtru nastavení, které jsou pro nasazení do emulátoru služby výpočty v Azure][add_acs_filter_lib_emulator]
11. Klikněte na **Dokončit**.
12. Klikněte na tlačítko **Ano** při předání s dialogové okno pole s oznámením, že se vytvoří soubor web.xml.
13. Klikněte na tlačítko **OK** zavřete **cesta sestavení Java** dialogové okno.

## <a name="deploy-to-the-compute-emulator"></a>Nasazení do emulátoru služby výpočty v
1. V prostředí Eclipse v prohlížeči projektu klikněte pravým tlačítkem na **MyACSHelloWorld**, klikněte na tlačítko **Azure**a potom klikněte na **balíček pro Azure**.
2. Pro **název projektu**, typ **MyAzureACSProject** a klikněte na tlačítko **Další**.
3. Vyberte JDK a aplikačního serveru. (Tyto kroky jsou podrobně popsány v [vytvoření aplikace Hello World služby Azure v prostředí Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) kurzu).
4. Klikněte na **Dokončit**.
5. Klikněte **spustit v emulátoru Azure** tlačítko.
6. Po spuštění webové aplikace v jazyce Java v emulátoru služby výpočty v, zavřete všechny instance prohlížeče (tak, aby všechny aktuální relace prohlížeče nezpůsobují konflikt s testováním přihlášení služby ACS).
7. Spusťte aplikaci otevřením <http://localhost:8080/MyACSHelloWorld/> v prohlížeči (nebo <https://localhost:8080/MyACSHelloWorld/> v případě, že je zaškrtnuté **připojení vyžadují HTTPS**). Jste vyzváni k přihlášení Windows Live ID a potom je třeba vzít na návratovou adresu URL zadanou pro aplikace předávající strany.
8. Klepněte na tlačítko zobrazení vaší aplikace **resetovat emulátoru Azure** tlačítko.

## <a name="deploy-to-azure"></a>Nasazení do Azure
Pokud chcete nasadit do Azure, budete muset změnit sféry předávající strany a návratovou adresu URL pro obor názvů služby ACS.

1. V rámci portálu pro správu Azure v **upravit aplikaci předávající strany** stránky, upravte **sféry** jako adresu URL nasazené lokality. Nahraďte **příklad** s název DNS zadaný pro vaše nasazení.
   
    ![Předávající strany sféru pro použití v produkčním prostředí][relying_party_realm_production]
2. Upravit **adresa URL pro návrat** jako adresu URL aplikace. Nahraďte **příklad** s název DNS zadaný pro vaše nasazení.
   
    ![Předávající strany návratovou adresu URL pro použití v produkčním prostředí][relying_party_return_url_production]
3. Klikněte na tlačítko **Uložit** uložit vaše aktualizované odpovídajících stran sféry a vrátíte se změny adresy URL.
4. Zachovat **přihlašovací stránky integrace** stránku v prohlížeči otevřít, budete muset krátce kopírovat.
5. V prostředí Eclipse v prohlížeči projektu klikněte pravým tlačítkem na **MyACSHelloWorld**, klikněte na tlačítko **cesta sestavení**a potom klikněte na **konfigurovat cestu sestavení**.
6. Klikněte **knihovny** , klikněte na **filtru služeb řízení přístupu Azure**a potom klikněte na **upravit**.
7. Pomocí prohlížeče otevřít k **přihlašovací stránky integrace** stránky portálu pro správu, Kopírovat adresu URL uvedené v **možnost 1: odkaz na stránku přihlášení hostované služby ACS** pole a vložte ji do **služby ACS Koncový bod ověřování** pole Eclipse dialogového okna.
8. Pomocí prohlížeče otevřít k **upravit aplikaci předávající strany** stránky portálu pro správu, Kopírovat adresu URL uvedené v **sféry** pole a vložte ji do **předávající strany sféry**pole Eclipse dialogového okna.
9. V rámci **zabezpečení** části dialogového okna Eclipse, pokud chcete použít stávající certifikát, klikněte na tlačítko **Procházet**, přejděte k certifikátu, kterou chcete použít, vyberte ho a klikněte na tlačítko **otevřete**. Nebo, pokud chcete vytvořit nový certifikát, klikněte na tlačítko **nový** zobrazíte **nový certifikát** dialogové okno, zadejte heslo, název souboru .cer a název souboru .pfx pro nový certifikát.
10. Zachovat **vložení certifikát do souboru WAR** zaškrtnuto, za předpokladu, že se má vložit certifikát v souboru WAR.
11. [Nepovinné] Zachovat **připojení vyžadují HTTPS** zaškrtnutí. Pokud nastavíte tuto možnost, budete potřebovat pro přístup k aplikaci pomocí protokolu HTTPS. Pokud nechcete, aby tak, aby vyžadovala připojení prostřednictvím protokolu HTTPS, zrušte tuto možnost.
12. Pro nasazení do Azure bude vypadat podobně jako následující nastavení filtru ACS Azure.
    
    ![Nastavení Azure ACS filtru pro produkční nasazení][add_acs_filter_lib_production]
13. Klikněte na tlačítko **Dokončit** zavřete **upravit knihovnu** dialogové okno.
14. Klikněte na tlačítko **OK** zavřete **vlastnosti pro MyACSHelloWorld** dialogové okno.
15. V prostředí Eclipse, klikněte **publikovat do cloudu Azure** tlačítko. Reagovat na výzvy, podobné jako v **pro nasazení aplikace do Azure** části [vytvoření aplikace Hello World služby Azure v prostředí Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) tématu. 

Po nasazení webové aplikace, zavřete všechny relace prohlížeče otevřete, spuštění webové aplikace a vám měla zobrazit výzva k přihlášení pomocí přihlašovacích údajů Windows Live ID, za nímž následuje odesílána návratovou adresu URL aplikace předávající strany.

Po dokončení pomocí aplikace Hello World služby ACS, nezapomeňte odstranit nasazení (můžete naučit odstranění nasazení v [vytvoření aplikace Hello World služby Azure v prostředí Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) tématu).

## <a name="next_steps"></a>Další kroky
Prozkoumání z zabezpečení Assertion SAML (Markup Language) vrácená službou ACS do vaší aplikace, najdete v části [zobrazení SAML vrácený službě Řízení přístupu Azure][How to view SAML returned by the Azure Access Control Service]. K dalšímu prozkoumat funkce služby ACS a experimentovat s sofistikovanější scénáři, najdete v tématu [2.0 služby Řízení přístupu][Access Control Service 2.0].

Navíc tento příklad používá **vložení certifikát do souboru WAR** možnost. Tato možnost usnadňuje nasazení certifikátu. Pokud chcete místo toho oddělit podpisový certifikát od souboru WAR, můžete použít takto:

1. V rámci **zabezpečení** části **filtru služeb řízení přístupu Azure** dialogové okno, typ **${env. JAVA_HOME}/mycert.cer** a zrušte zaškrtnutí políčka **vložení certifikát do souboru WAR**. (Pokud název souboru certifikátu se liší, upravte Můj_certifikát.cer.) Klikněte na tlačítko **Dokončit** zavřete dialogové okno.
2. Zkopírujte certifikát jako součást ve vašem nasazení: V prohlížeči na Eclipse projektu, rozbalte položku **MyAzureACSProject**, klikněte pravým tlačítkem na **WorkerRole1**, klikněte na tlačítko **vlastnosti**, Rozbalte položku **Role v Azure**a klikněte na tlačítko **součásti**.
3. Klikněte na tlačítko **Add** (Přidat).
4. V rámci **přidat součást** dialogové okno:
   
   1. V **Import** části:
      1. Použít **souboru** tlačítko přejděte k certifikátu, kterou chcete použít. 
      2. Pro **metoda**, vyberte **kopie**.
   2. Pro **název jako**, klikněte na textové pole a přijměte výchozí název.
   3. V **nasadit** části:
      1. Pro **metoda**, vyberte **kopie**.
      2. Pro **do adresáře**, typ **JAVA_HOME %**.
   4. Vaše **přidat součást** dialogové okno by měl vypadat podobně jako následující.
      
       ![Přidat součást certifikátu][add_cert_component]
   5. Klikněte na **OK**.

V tomto okamžiku by váš certifikát zahrnuty ve vašem nasazení. Všimněte si, že bez ohledu na to, zda vložení certifikát do souboru WAR, nebo ji přidat jako součást na vaše nasazení, je potřeba nahrajte certifikát do vašeho oboru názvů, jak je popsáno v [nahrání certifikátu do vašeho oboru názvů služby ACS] [ Upload a certificate to your ACS namespace] části.

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Upload a certificate to your ACS namespace]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[project website]: http://wastarterkit4java.codeplex.com/releases/view/61026
[How to view SAML returned by the Azure Access Control Service]: active-directory-java-view-saml-returned-by-access-control.md
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure Management Portal]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png

