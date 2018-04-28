---
title: Řešení potíží s domény a problémy s certifikátem SSL ve službě Azure web apps | Microsoft Docs
description: Řešení potíží s domény a problémy s certifikátem SSL ve službě Azure web apps
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: genli
ms.openlocfilehash: ba21475b771f1688c0a3f2f34c8d961fba5cd182
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Řešení potíží s domény a problémy s certifikátem SSL ve službě Azure web apps

V tomto článku jsou uvedeny běžné problémy, které se můžete setkat při konfiguraci domény nebo certifikát SSL pro vaše webové aplikace Azure. Také popisuje možné příčiny a řešení těchto problémů.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [MSDN Azure a fóra Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete také soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a klikněte na **získat podporu**.

## <a name="certificate-problems"></a>Problémy s certifikátem

### <a name="unable-to-add-bind-ssl-certificate-to-a-web-app"></a>Nelze přidat vazby certifikátu SSL pro webovou aplikaci 

### <a name="symptom"></a>Příznaky

Když přidáte vazby SSL, zobrazí se následující chybová zpráva:

**Nepodařilo se přidat vazbu SSL. Nelze nastavit certifikátu pro stávající virtuální IP adresy, protože jiná VIP již používá tento certifikáty.**

### <a name="cause"></a>Příčina

Tomuto problému může dojít, pokud máte víc vazeb SSL založenou na protokolu IP pro stejnou IP adresu napříč více webových aplikací. Webové aplikace A má například založenou na protokolu IP protokol SSL starý certifikát. Webové aplikace B přes SSL na základě IP pomocí nového certifikátu pro stejnou IP adresu. Když aktualizujete vazbu SSL webové aplikace s novým certifikátem, se nezdaří s touto chybou od stejné IP adresy se používá pro jiné aplikace. 

### <a name="solution"></a>Řešení 

Chcete-li tento problém vyřešit, použijte jednu z následujících metod:

- Odstraňte vazbu založená na IP ve webové aplikaci, která používá starý certifikát. 
- Vytvořte novou vazbu SSL založenou na protokolu IP, který používá nový certifikát.

### <a name="unable-to-delete-a-certificate"></a>Nelze odstranit certifikát 

### <a name="symptom"></a>Příznaky

Když se pokusíte odstranit certifikát, zobrazí se následující chybová zpráva:

**Nelze odstranit certifikát, protože je aktuálně používána v vazbu SSL. Vazba SSL musí být odstraněny, než budete moct odstranit certifikát.**

### <a name="cause"></a>Příčina

Tento problém může dojít, pokud tento certifikát se používá v jiné webové aplikaci.

### <a name="solution"></a>Řešení

Odeberte vazbu SSL pro tento certifikát z webové aplikace. Potom se pokuste odstranit certifikát. Pokud je certifikát stále nelze odstranit, vymažte mezipaměť prohlížeče Internet, otevřete portál Azure nové okno prohlížeče. A potom se pokuste odstranit certifikát.

### <a name="unable-to-purchase-an-app-service-certificate"></a>Nelze zakoupit certifikát služby App Service 

### <a name="symptom"></a>Příznaky
Není možné dokoupit [certifikát služby App Service](./web-sites-purchase-ssl-web-site.md) z portálu Azure.

### <a name="cause-and-solution"></a>Příčina a řešení
Tento problém může vzniknout z některého z následujících důvodů:

- Plán služby App Service je "Free" nebo "Sdílené". Nepodporujeme SSL pro tyto cenové úrovně. 

    **Řešení**: upgradovat plán služby App Service pro webovou aplikaci na "Standard".

- Předplatné nemá platná platební karta.

    **Řešení**: do svého předplatného přidáte platná platební karta. 

- Nabídky předplatného nepodporuje nákup certifikát služby App Service, například studenty Microsoft.  

    **Řešení**: Upgrade vašeho předplatného. 

- Předplatné bylo dosaženo maximálního limitu nákupy, které jsou povoleny na předplatném.

    **Řešení**: certifikáty App Service mají limit 10 nákupy certifikát pro typy platím jako přejděte a EA odběry. Pro ostatní typy předplatného se limit je 3. Chcete-li tento limit zvýšit, kontaktujte [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Certifikát služby App Service byla označena jako podvodů. Zobrazí se následující chybová zpráva: "váš certifikát byl označen příznakem pro možném podvodu. Žádost aktuálně probíhá kontrola. Pokud certifikát nestane použitelné do 24 hodin".

    **Řešení**: Pokud certifikát je označena jako podvodů a nepřeložilo po 24 hodinách, postupujte podle těchto kroků:

    1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
    2. Přejděte na **služby App Service Certificate**, vyberte certifikát.
    3. Vyberte **konfigurace certifikátu** > **krok 2: ověření** > **ověření domény**. Tím se odešle e-mailová oznámení k poskytovateli Azure certifikát pro vyřešení problému.

## <a name="domain-problems"></a>Problémy domény

### <a name="purchased-ssl-certificate-for-wrong-domain"></a>Zakoupili certifikát SSL pro nesprávné domény

### <a name="symptom"></a>Příznaky

Jste zakoupili certifikát služby App Service pro nesprávné domény a nejde aktualizovat certifikát, který chcete použít správné doméně.

### <a name="solution"></a>Řešení

- Odstraňte tento certifikát a pak Zakupte nový certifikát.
- Pokud aktuální certifikát, který se používá nesprávný domény je ve stavu "Vydáno", pak bude také se fakturuje pro daný certifikát. Certifikáty App Service nejsou nevratný, ale můžou kontaktovat [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) zobrazíte, jestli existují další možnosti. 

### <a name="app-service-certificate-was-renewed-but-still-shows-the-old-certificate"></a>Certifikát služby App Service byl obnoven, ale stále zobrazuje starý certifikát 

### <a name="symptom"></a>Příznaky

Certifikát služby App Service byl obnoven ale webové aplikace, kterou používá certifikát služby App Service stále používá starý certifikát. Navíc se vám zobrazila upozornění, že je vyžadována protokol HTTPS.

### <a name="cause"></a>Příčina 
Webové aplikace spustí úlohu na pozadí každých 8 hodin a synchronizuje prostředek certifikátu, pokud nedošlo k žádným změnám. Proto když otáčet nebo aktualizovat certifikát, někdy aplikace stále načítá starý certifikát a ne nově aktualizovaných certifikát. Je to proto, že úloha synchronizace prostředků certifikát ještě nebyl spuštěn. 
 
### <a name="solution"></a>Řešení

Můžete vynutit synchronizace certifikátu:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com). Vyberte **služby App service Certificate**a pak vyberte certifikát.
2. Klikněte na tlačítko **změna hodnoty klíče a synchronizovat**a potom klikněte na **synchronizace**. Tato akce trvá delší dobu pro dokončení. 
3. Po dokončení synchronizace zobrazit následující upozornění: "Úspěšně aktualizovat všechny prostředky nejnovější certifikátem".

### <a name="domain-verification-is-not-working"></a>Ověření domény není funkční. 

### <a name="symptom"></a>Příznaky 
Certifikát služby App Service vyžaduje ověření domény, než se certifikát připravené k použití. Když kliknete na tlačítko **ověřte**, proces selže.

### <a name="solution"></a>Řešení
Ručně ověřte svoji doménu tak, že přidáte záznam TXT:
 
1.  Přejděte k poskytovateli služby DNS (Domain Name), který je hostitelem název vaší domény.
2.  Přidejte záznam TXT pro vaši doménu, která používá hodnotu tokenu domény, které se zobrazí na portálu Azure. 

Počkejte několik minut pro šíření záznamů DNS spustit, a pak klikněte na **aktualizovat** tlačítko pro aktivaci ověření. 

Alternativní metoda ručně ověřit je "metody webovou stránku HTML", která slouží k povolení certifikační autority do potvrdili vlastnictví domény, který je vydán pro domény.

1.  Vytvoření souboru HTML, který je s názvem {domény ověření tokenu} .html. 
2.  Obsah tohoto souboru musí být hodnota tokenu ověření domény.
3.  Odeslat tento soubor v kořenovém adresáři webového serveru, který je hostitelem vaší doméně
4.  Klikněte na tlačítko **aktualizovat** zkontrolovat stav certifikátu. Může trvat několik minut na dokončení ověření.

Například při nákupu standardní certifikát pro azure.com pomocí tokenu ověření domény '1234abcd' pak webové žádosti na http://azure.com/1234abcd.html by měla vrátit 1234abcd. 

> [!IMPORTANT]
> Pořadí certifikát má pouze 15 dní na provedení operace ověření domény. Po 15 dnech certifikátu byl odepřen certifikační autoritou a vám není účtován certifikát. V takovém případě odstraňte tento certifikát a zkuste to znovu.
>
> 

### <a name="unable-to-purchase-a-domain"></a>Chcete-li zakoupit domény nelze

### <a name="symptom"></a>Příznaky
Z webové aplikace nebo aplikaci služby domény nelze koupit doménu na portálu Azure.

### <a name="cause-and-solution"></a>Příčina a řešení

K tomuto problému dochází pro jednu z následujících důvodů:

- Žádná platební karta na předplatné Azure nebo neplatný platební karty.

    **Řešení**: Nemáte-li do svého předplatného přidáte platná platební karta.

- Pokud si nejste vlastník předplatného, nemusí mít oprávnění k zakoupení domény.

    **Řešení**: [přidat roli vlastníka](../billing/billing-add-change-azure-subscription-administrator.md) k účtu nebo kontaktu s správce předplatného a získejte oprávnění k nákupu domény.
- Dosáhli jste limitu pro nákup domén na vaše předplatné. Současný limit je 20.

    **Řešení**: na žádost o zvýšení limitu, kontaktujte [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Typ vašeho předplatného Azure nepodporuje nákup domény služby App Service.

    **Řešení**: Upgrade vašeho předplatného Azure na jiné typy předplatného, jako je například předplatné průběžnými platbami.

### <a name="unable-to-add-a-hostname-to-web-app"></a>Nelze přidat hostitele do webové aplikace 

### <a name="symptom"></a>Příznaky

Při přidání hostitele, proces selže, pokud chcete ověřit a ověření domény.

### <a name="cause"></a>Příčina 

K tomuto problému dochází pro jednu z následujících důvodů:

- Nemáte oprávnění k přidávání hostitele.

    **Řešení**: Zkontrolujte u správce předplatného, abyste měli jistotu, že máte oprávnění k přidávání hostitele.
- Nebylo možné ověřit vlastnictví domény.

    **Řešení**: Ověřte, jestli jsou správně nakonfigurované vaše CNAME nebo záznam. Vlastní domény mapovat do webové aplikace, vytvořte CNAME nebo A záznam. Pokud chcete použít kořenové domény, musíte použít záznamy A a TXT:

    |Typ záznamu|Hostitel|Přejděte na|
    |------|------|-----|
    |A|@|IP adresa pro webovou aplikaci|
    |TXT|@|< název aplikace >. azurewebsites.net|
    |CNAME|www|< název aplikace >. azurewebsites.net|

### <a name="dns-cannot-be-resolved"></a>DNS nelze přeložit.

### <a name="symptom"></a>Příznaky

Zobrazí chybová zpráva "záznam DNS nebyl nalezen".

### <a name="cause"></a>Příčina
K tomuto problému dochází pro jednu z následujících důvodů:

- Čas na dobu, Live (TTL) jestli nevypršela platnost. Zkontrolujte konfiguraci DNS pro doménu, abyste zjistili hodnota TTL a potom počkejte, než po dobu vypršení platnosti.
- Konfigurace DNS je nesprávná.

### <a name="solution"></a>Řešení
- Počkejte, než 48 hodin pro tento problém se vyřeší.
- Pokud ve své konfiguraci DNS může změnit nastavení TTL, změňte hodnotu na 5 minut, zda se podařilo problém vyřešit.
- Použití [WhatsmyDNS.net](https://www.whatsmydns.net/) k ověření, že vaše doména odkazují na IP adresu webové aplikace. Pokud ne, konfigurovat záznam A správnou IP adresu webové aplikace.

### <a name="restore-a-deleted-domain"></a>Obnovení odstraněného domény 

### <a name="symptom"></a>Příznaky
Vaše doména je už nebude viditelné v portálu Azure.

### <a name="cause"></a>Příčina 
Domény omylem odstraněný vlastníkem předplatného.

### <a name="solution"></a>Řešení
Pokud vaše doména byla odstraněna před méně než 7 dní, doménu ještě nezačala proces odstraňování. V takovém případě můžete koupit ve stejné doméně znovu na portálu Azure v rámci stejného předplatného (zkontrolujte, že do vyhledávacího pole zadejte název přesný domény). Vám nebude nic účtováno znovu pro tuto doménu. Pokud doména byla odstraněna před více než 7 dní, kontaktujte prosím [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) nápovědu k obnovení domény.

### <a name="custom-domain-returns-404-or-site-inaccessible"></a>Vlastní domény vrátí 404 nebo lokality nedostupné 

### <a name="symptom"></a>Příznaky

Když přejdete na web pomocí vlastní název domény, zobrazí se následující chybová zpráva:

**Chyba 404webové aplikace nebyla nalezena.**


### <a name="cause-and-solution"></a>Příčina a řešení

**Příčina 1** 

Vlastní domény, který jste nakonfigurovali chybí záznam CNAME nebo A. 

**Řešení pro příčina 1**

- Pokud jste přidali záznam A, ujistěte se, že je taky přidaný záznam TXT. Další informace najdete v tématu [vytvořit the--record](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Pokud nemáte použít kořenové domény pro vaši webovou aplikaci, se doporučuje použít záznam CNAME místo záznam.
- Záznam CNAME i A nepoužívejte pro stejné domény. To může způsobit konflikt a zabrání domény řešení. 

**Příčina 2** 

Internetového prohlížeče může být stále ukládání starou IP adresu pro vaši doménu. 

**Řešení pro příčina 2**

Zrušte zaškrtnutí v prohlížeči. Pro zařízení s Windows, můžete spustit příkaz `ipconfig /flushdns`. Použití [WhatsmyDNS.net](https://www.whatsmydns.net/) k ověření, že vaše doména odkazují na IP adresu webové aplikace. 

### <a name="unable-to-add-subdomain"></a>Nelze přidat subdomény 

### <a name="symptom"></a>Příznaky

Nový název hostitele nelze přidat do webové aplikace přiřadit subdomény.

### <a name="solution"></a>Řešení

- Zeptejte se správce předplatného, abyste měli jistotu, že máte oprávnění k přidání hostitele do webové aplikace.
- Pokud potřebujete další subdomén, doporučujeme změnit hostování domény do Azure DNS. Pomocí Azure DNS, můžete přidat 500 názvy hostitelů do vaší webové aplikace. Další informace najdete v tématu [přidat doménu dílčí](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 


















