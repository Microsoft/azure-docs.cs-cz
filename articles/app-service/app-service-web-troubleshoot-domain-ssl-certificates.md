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
ms.openlocfilehash: 59a9011edef49494288716ab16f30e28e440293b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Řešení potíží s domény a problémy s certifikátem SSL ve službě Azure web apps

V tomto článku jsou uvedeny běžné problémy, které se můžete setkat při konfiguraci domény nebo certifikát SSL pro vaše webové aplikace Azure. Také popisuje možné příčiny a řešení těchto problémů.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="certificate-problems"></a>Problémy s certifikátem

### <a name="you-cant-add-an-ssl-certificate-binding-to-a-web-app"></a>Nelze přidat vazbu certifikátu SSL pro webovou aplikaci 

#### <a name="symptom"></a>Příznaky

Když přidáte vazby SSL, zobrazí se následující chybová zpráva:

"Nepodařilo se přidat vazbu SSL. Nelze nastavit certifikátu pro stávající virtuální IP adresy protože jiné VIP už používá tento certifikát."

#### <a name="cause"></a>Příčina

Tomuto problému může dojít, pokud máte víc vazeb SSL založenou na protokolu IP pro stejnou IP adresu napříč více webových aplikací. Webové aplikace A má například protokolem SSL založenou na protokolu IP s starý certifikát. Webovou aplikaci B má protokolem SSL založenou na protokolu IP pomocí nového certifikátu pro stejnou IP adresu. Když aktualizujete vazbu SSL webové aplikace pomocí nového certifikátu, selže s touto chybou protože stejnou IP adresu se používá pro jiné aplikace. 

#### <a name="solution"></a>Řešení 

Chcete-li tento problém vyřešit, použijte jednu z následujících metod:

- Odstraňte vazbu založená na IP ve webové aplikaci, která používá starý certifikát. 
- Vytvořte novou vazbu SSL založenou na protokolu IP, který používá nový certifikát.

### <a name="you-cant-delete-a-certificate"></a>Certifikát nelze odstranit 

#### <a name="symptom"></a>Příznaky

Když se pokusíte odstranit certifikát, zobrazí se následující chybová zpráva:

"Nelze odstranit certifikát, protože je aktuálně používána v vazbu SSL. Vazba SSL musí odebrat před odstraněním certifikátu."

#### <a name="cause"></a>Příčina

Tento problém se může vyskytnout, pokud jiné webové aplikace používá certifikát.

#### <a name="solution"></a>Řešení

Odeberte vazbu SSL pro tento certifikát z webové aplikace. Potom se pokuste odstranit certifikát. Pokud je certifikát stále nelze odstranit, vymažte mezipaměť prohlížeče internet a znovu otevřete portál Azure nové okno prohlížeče. Potom se pokuste odstranit certifikát.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Není možné dokoupit certifikát služby App Service 

#### <a name="symptom"></a>Příznaky
Není možné dokoupit [Azure App Service certificate](./web-sites-purchase-ssl-web-site.md) z portálu Azure.

#### <a name="cause-and-solution"></a>Příčina a řešení
Tento problém může vzniknout z některého z následujících důvodů:

- Plán služby App Service je volné nebo sdílené. Tyto cenové úrovně nepodporují SSL. 

    **Řešení**: upgradovat plán služby App Service pro webovou aplikaci na Standard.

- Předplatné nemá platná platební karta.

    **Řešení**: do svého předplatného přidáte platná platební karta. 

- Nabídky předplatného nepodporuje nákup certifikát služby App Service, například studenty Microsoft.  

    **Řešení**: Upgrade vašeho předplatného. 

- Odběr byl dosažen limit nákupy, které jsou povoleny na předplatném.

    **Řešení**: mít maximálně 10 nákupy certifikát pro typy předplatného průběžné platby a EA certifikáty služby App Service. Pro ostatní typy předplatného se limit je 3. Chcete-li tento limit zvýšit, kontaktujte [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Certifikát služby App Service byla označena jako podvodů. Se vám zobrazila tato chybová zpráva: "váš certifikát byl označen příznakem pro možném podvodu. Žádost aktuálně probíhá kontrola. Pokud certifikát není začnou použitelné do 24 hodin, kontaktujte prosím podporu Azure."

    **Řešení**: Pokud certifikát je označena jako podvodů a není vyřešen po 24 hodinách, postupujte podle těchto kroků:

    1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
    2. Přejděte na **služby App Service Certificate**a vyberte certifikát.
    3. Vyberte **konfigurace certifikátu** > **krok 2: ověření** > **ověření domény**. Tento krok odešle e-mailová oznámení k poskytovateli Azure certifikát pro vyřešení problému.

## <a name="domain-problems"></a>Problémy domény

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Jste zakoupili certifikát protokolu SSL pro nesprávné domény

#### <a name="symptom"></a>Příznaky

Jste zakoupili certifikát služby App Service pro chybný doménu. Nelze aktualizovat certifikát, který chcete použít správné doméně.

#### <a name="solution"></a>Řešení

Odstraňte tento certifikát a pak Zakupte nový certifikát.

Pokud aktuální certifikát, který se používá nesprávný domény je ve stavu "Vydáno", také platit budete pro daný certifikát. Certifikáty App Service nejsou nevratný, ale můžou kontaktovat [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) zobrazíte, jestli existují další možnosti. 

### <a name="an-app-service-certificate-was-renewed-but-the-web-app-shows-the-old-certificate"></a>Byl obnoven na server certifikát služby App Service, ale webová aplikace zobrazuje starý certifikát 

#### <a name="symptom"></a>Příznaky

Certifikát služby App Service byl obnoven, ale webové aplikace, kterou používá certifikát služby App Service je stále používá starý certifikát. Navíc se vám zobrazila upozornění, že je vyžadována protokol HTTPS.

#### <a name="cause"></a>Příčina 
Funkce Web Apps služby Azure App Service spouští úlohu na pozadí každých 8 hodin a synchronizuje prostředek certifikátu, pokud nedošlo k žádným změnám. Když otáčet nebo aktualizovat certifikát, někdy aplikace stále načítá starý certifikát a ne nově aktualizovaných certifikát. Důvodem je, že úloha synchronizace prostředků certifikát nebyl spuštěn ještě. 
 
#### <a name="solution"></a>Řešení

Můžete vynutit synchronizace certifikátu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **služby App Service Certificate**a pak vyberte certifikát.
2. Vyberte **změna hodnoty klíče a synchronizovat**a potom vyberte **synchronizace**. Synchronizace trvá delší dobu pro dokončení. 
3. Po dokončení synchronizace zobrazit následující upozornění: "Aktualizace úspěšně všechny prostředky pomocí nejnovější certifikátu."

### <a name="domain-verification-is-not-working"></a>Ověření domény není funkční. 

#### <a name="symptom"></a>Příznaky 
Certifikát služby App Service vyžaduje ověření domény, než se certifikát připravené k použití. Když vyberete **ověřte**, proces selže.

#### <a name="solution"></a>Řešení
Ručně ověřte svoji doménu tak, že přidáte záznam TXT:
 
1.  Přejděte k poskytovateli služby DNS (Domain Name), který je hostitelem název vaší domény.
2.  Přidejte záznam TXT pro vaši doménu, která používá hodnotu tokenu domény, které se zobrazí na portálu Azure. 

Počkejte několik minut pro šíření záznamů DNS spustit, a potom vyberte **aktualizovat** tlačítko pro aktivaci ověření. 

Jako alternativu můžete použít metodu webovou stránku HTML ručně ověřit doménu. Tato metoda umožňuje certifikační autority do potvrdili vlastnictví domény domény, který certifikát je vystavený pro.

1.  Vytvoření souboru HTML, který je s názvem {tokenu ověření domény} .html. Obsah tohoto souboru musí být hodnota tokenu ověření domény.
3.  Odešlete tento soubor v kořenovém adresáři webového serveru, který je hostitelem vaší domény.
4.  Vyberte **aktualizovat** zkontrolovat stav certifikátu. Může trvat několik minut na dokončení ověření.

Například pokud jste zakoupení standardní certifikátu pro azure.com s tokenu 1234abcd ověření domény, webové žádosti na http://azure.com/1234abcd.html by měla vrátit 1234abcd. 

> [!IMPORTANT]
> Pořadí certifikát má pouze 15 dní na provedení operace ověření domény. Po 15 dnech odmítne certifikační autority certifikátu a vám není účtován certifikát. V takovém případě odstraňte tento certifikát a zkuste to znovu.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Není možné dokoupit domény

#### <a name="symptom"></a>Příznaky
Nelze zakoupení domény z domény webové aplikace nebo služby App Service na portálu Azure.

#### <a name="cause-and-solution"></a>Příčina a řešení

K tomuto problému dochází pro jednu z následujících důvodů:

- Neexistuje žádná platební karta na předplatné Azure nebo z platební karty je neplatný.

    **Řešení**: do svého předplatného přidáte platná platební karta.

- Nejste se vlastník předplatného, takže nemáte oprávnění k nákupu domény.

    **Řešení**: [přidat roli vlastníka](../billing/billing-add-change-azure-subscription-administrator.md) ke svému účtu. Nebo se obraťte na správce předplatného získat oprávnění k nákupu domény.
- Dosáhli jste limitu pro nákup domén na vaše předplatné. Současný limit je 20.

    **Řešení**: Chcete-li požádat o zvýšení limitu, obraťte se na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Typ vašeho předplatného Azure nepodporuje nákup domény služby App Service.

    **Řešení**: Upgrade vašeho předplatného Azure k jinému typu předplatného, jako je například předplatné průběžnými platbami.

### <a name="you-cant-add-a-host-name-to-a-web-app"></a>Název hostitele nelze přidat do webové aplikace 

#### <a name="symptom"></a>Příznaky

Když přidáte název hostitele, proces selže, pokud chcete ověřit a ověření domény.

#### <a name="cause"></a>Příčina 

K tomuto problému dochází pro jednu z následujících důvodů:

- Nemáte oprávnění k přidávání název hostitele.

    **Řešení**: požádejte správce předplatného tak, abyste získali oprávnění k přidávání název hostitele.
- Nebylo možné ověřit vlastnictví domény.

    **Řešení**: Ověřte, zda je správně nakonfigurována vaše CNAME nebo záznam. Pokud chcete namapovat vlastní doménu do webové aplikace, vytvořte záznam CNAME nebo záznam. Pokud chcete použít kořenovou doménu, musíte použít záznamy A a TXT:

    |Typ záznamu|Hostitel|Přejděte na|
    |------|------|-----|
    |A|@|IP adresa pro webovou aplikaci|
    |TXT|@|< název aplikace >. azurewebsites.net|
    |CNAME|www|< název aplikace >. azurewebsites.net|

### <a name="dns-cant-be-resolved"></a>Nelze přeložit DNS

#### <a name="symptom"></a>Příznaky

Jste dostali se následující chybová zpráva:

"Záznam DNS nebyl nalezen."

#### <a name="cause"></a>Příčina
K tomuto problému dochází pro jednu z následujících důvodů:

- Čas na dobu, za provozu (TTL) jestli nevypršela platnost. Zkontrolujte konfiguraci DNS pro doménu, abyste zjistili hodnota TTL a potom počkejte, než po dobu vypršení platnosti.
- Konfigurace DNS je nesprávná.

#### <a name="solution"></a>Řešení
- Počkejte, než 48 hodin pro tento problém se vyřeší.
- Pokud ve své konfiguraci DNS může změnit nastavení TTL, změňte hodnotu na 5 minut, zda se podařilo problém vyřešit.
- Použití [WhatsmyDNS.net](https://www.whatsmydns.net/) k ověření, že doménu odkazují na IP adresu webové aplikace. Pokud tomu tak není, konfigurovat záznam A správnou IP adresu webové aplikace.

### <a name="you-need-to-restore-a-deleted-domain"></a>Je třeba obnovit odstraněné domény 

#### <a name="symptom"></a>Příznaky
Vaše doména je už nebude viditelné v portálu Azure.

#### <a name="cause"></a>Příčina 
Vlastník předplatného může omylem odstranili domény.

#### <a name="solution"></a>Řešení
Pokud vaše doména byla odstraněna před méně než 7 dní, doménu ještě nezačala proces odstraňování. V takovém případě můžete koupit ve stejné doméně znovu na portálu Azure v rámci stejného předplatného. (Nezapomeňte do vyhledávacího pole zadejte název přesný domény.) Vám nebude nic účtováno znovu pro tuto doménu. Pokud doména byla odstraněna před více než 7 dní, obraťte se na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) nápovědu k obnovení domény.

### <a name="a-custom-domain-returns-a-404-error"></a>Vlastní doménu vrátí chybu 404 

#### <a name="symptom"></a>Příznaky

Když přejdete na web pomocí vlastní název domény, zobrazí se následující chybová zpráva:

"Chyba 404 webové aplikace nebyl nalezen."


#### <a name="cause-and-solution"></a>Příčina a řešení

**Příčina 1** 

Vlastní domény, který jste nakonfigurovali chybí záznam CNAME nebo A. 

**Řešení pro příčina 1**

- Pokud jste přidali záznam A, ujistěte se, že je taky přidaný záznam TXT. Další informace najdete v tématu [vytvořte záznam a](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Pokud nemáte používat kořenové domény pro vaši webovou aplikaci, doporučujeme použít záznam CNAME místo záznam.
- Nepoužívejte záznam CNAME a záznam A ve stejné doméně. To může způsobit konflikt a zabrání přeloženy domény. 

**Příčina 2** 

Internetového prohlížeče může být stále ukládání starou IP adresu pro vaši doménu. 

**Řešení pro příčina 2**

Zrušte zaškrtnutí v prohlížeči. Pro zařízení s Windows, můžete spustit příkaz `ipconfig /flushdns`. Použití [WhatsmyDNS.net](https://www.whatsmydns.net/) k ověření, že doménu odkazují na IP adresu webové aplikace. 

### <a name="you-cant-add-a-subdomain"></a>Nelze přidat subdoména 

#### <a name="symptom"></a>Příznaky

Nový název hostitele nelze přidat do webové aplikace přiřadit subdoména.

#### <a name="solution"></a>Řešení

- Zeptejte se správce předplatného, abyste měli jistotu, že máte oprávnění k přidání názvu hostitele do webové aplikace.
- Pokud potřebujete další subdomény, doporučujeme změnit hostování domény do Azure DNS. Pomocí Azure DNS, můžete přidat 500 názvy hostitelů do vaší webové aplikace. Další informace najdete v tématu [přidat subdoména](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 


















