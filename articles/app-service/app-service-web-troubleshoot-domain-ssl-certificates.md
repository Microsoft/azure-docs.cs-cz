---
title: Řešení potíží s domény a certifikáty SSL – Azure App Service | Dokumentace Microsoftu
description: Řešení potíží s doménou a problémy s certifikátem protokolu SSL ve službě Azure web apps
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
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 726bc78532cfe621eb3f3787aa05a7a54571a8c3
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251602"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Řešení potíží s doménou a problémy s certifikátem protokolu SSL ve službě Azure web apps

Tento článek uvádí běžné problémy, které se můžete setkat při konfiguraci domény nebo certifikát SSL pro službu Azure web apps. Také popisuje možné příčiny a řešení těchto problémů.

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="certificate-problems"></a>Problémy s certifikátem

### <a name="you-cant-add-an-ssl-certificate-binding-to-a-web-app"></a>Nelze přidat vazbu certifikátu SSL do webové aplikace 

#### <a name="symptom"></a>Příznak

Když přidáte vazby SSL, zobrazí se následující chybová zpráva:

"Nepovedlo se přidat vazbu SSL. Nelze nastavit certifikát pro stávající virtuální IP adresy protože jiná virtuální IP adresa už používá tento certifikát."

#### <a name="cause"></a>Příčina

Tomuto problému může dojít, pokud máte více vazeb SSL založených na protokolu IP pro stejnou IP adresu mezi několik webových aplikací. Například webové aplikace A má se původní certifikát SSL na základě IP adresy. Webová aplikace B má SSL založené na protokolu IP pomocí nového certifikátu pro stejnou IP adresu. Při aktualizaci vazby SSL webové aplikace s novým certifikátem selže s touto chybou vzhledem k tomu, že stejné IP adresy se používá pro jiné aplikace. 

#### <a name="solution"></a>Řešení 

Chcete-li vyřešit tento problém, použijte jednu z následujících metod:

- Odstranění vazby SSL založené na protokolu IP ve webové aplikaci, která používá starý certifikát. 
- Vytvořte novou vazbu SSL na základě IP adresy, které používá nový certifikát.

### <a name="you-cant-delete-a-certificate"></a>Nelze odstranit certifikát 

#### <a name="symptom"></a>Příznak

Při pokusu o odstranění certifikátu, zobrazí se následující chybová zpráva:

"Se nepodařilo odstranit certifikát, protože se aktuálně používá v vazby SSL. Vazba SSL musí být odstraněny než budete moct odstranit certifikát."

#### <a name="cause"></a>Příčina

Tomuto problému může dojít, pokud jiné webové aplikace používá certifikát.

#### <a name="solution"></a>Řešení

Odeberte vazbu SSL pro tento certifikát z webové aplikace. Pokuste se odstranit certifikát. Pokud stále nelze odstranit certifikát, vymazat mezipaměť internetového prohlížeče a znovu otevřít na webu Azure portal v novém okně prohlížeče. Pokuste se odstranit certifikát.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Není možné koupit na server certifikát služby App Service 

#### <a name="symptom"></a>Příznak
Není možné koupit [služby Azure App Service certificate](./web-sites-purchase-ssl-web-site.md) z portálu Azure portal.

#### <a name="cause-and-solution"></a>Příčina a řešení
Tento problém může vzniknout z některého z následujících důvodů:

- Plán služby App Service je Free nebo Shared. Tyto cenové úrovně nepodporují SSL. 

    **Řešení**: Upgradujte plán služby App Service pro webové aplikace na úroveň Standard.

- Předplatné nemá uvedenou platnou platební kartu.

    **Řešení**: Ke svému předplatnému přidáte uvedenou platnou platební kartu. 

- Nabídky předplatného nepodporuje si koupíte certifikát App Service, jako je například Microsoft Student.  

    **Řešení**: Upgradujte svoje předplatné. 

- Odběr byl dosažen limit nákupy, které jsou povoleny v rámci předplatného.

    **Řešení**: Certifikáty služby App Service mají omezení na 10 nákupy certifikát pro typy předplatného s průběžnými platbami a EA. Limit pro ostatní typy předplatného je 3. Limit zvýšit, obraťte se na [podpory Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Certifikát App Service byla označena jako podvod. Dostanete se následující chybová zpráva: "Váš certifikát se označil jako potenciálně podvodný. Požadavek je právě probíhá kontrola. Pokud certifikát není autentický během 24 hodin, kontaktujte prosím podporu Azure. "

    **Řešení**: Pokud tento certifikát je označen jako podvodů a nevyřeší po 24 hodinách, postupujte podle těchto kroků:

    1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
    2. Přejděte na **služby App Service Certificate**a vyberte certifikát.
    3. Vyberte **konfigurace certifikátu** > **krok 2: Ověřte** > **ověření domény**. Tento krok odešle oznámení e-mailu certifikátů Azure poskytovatelem a vyřešit problém.

## <a name="domain-problems"></a>Problémy domény

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Jste zakoupili certifikát SSL pro nesprávnou doménou

#### <a name="symptom"></a>Příznak

Jste zakoupili certifikát App Service pro nesprávnou doménou. Nelze aktualizovat certifikát, který používáte správnou doménu.

#### <a name="solution"></a>Řešení

Odstranit tento certifikát a potom koupit nový certifikát.

Pokud aktuální certifikát, který používá nesprávnou doménou je ve stavu "Vydáno", můžete také účtovat tohoto certifikátu. Certifikáty služby App Service jsou nevratné, ale můžete kontaktovat [podpory Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , jestli existují další možnosti. 

### <a name="an-app-service-certificate-was-renewed-but-the-web-app-shows-the-old-certificate"></a>Bylo obnoveno na server certifikát služby App Service, ale webová aplikace se zobrazuje starý certifikát 

#### <a name="symptom"></a>Příznak

Platnost certifikátu služby App Service se obnovila, ale webovou aplikaci, která používá certifikát App Service stále používá starý certifikát. Navíc dostanete upozornění, že se musí používat protokol HTTPS.

#### <a name="cause"></a>Příčina 
Funkce Web Apps služby Azure App Service spouští úlohy na pozadí každých 8 hodin a synchronizuje prostředek certifikátu, pokud nedošlo k žádným změnám. Při otočení nebo aktualizovat certifikát, někdy aplikace stále načítá starý certifikát a není nově aktualizovaný certifikát. Důvodem je, že nebyla dosud spuštěna úloha synchronizovat prostředek certifikátu. 
 
#### <a name="solution"></a>Řešení

Můžete vynutit synchronizaci certifikátu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **služby App Service Certificate**a pak vyberte certifikát.
2. Vyberte **obnovit klíč a synchronizovat**a pak vyberte **synchronizace**. Synchronizace nějakou dobu dokončení trvá. 
3. Po dokončení synchronizace se zobrazí následující oznámení: "Úspěšně aktualizovány všechny prostředky používaly nejnovější certifikát."

### <a name="domain-verification-is-not-working"></a>Ověření domény nefunguje 

#### <a name="symptom"></a>Příznak 
Certifikát App Service vyžaduje ověření domény předtím, než se certifikát je připravený k použití. Když vyberete **ověřte**, proces selže.

#### <a name="solution"></a>Řešení
Přidejte záznam TXT ručně ověřte svoji doménu:
 
1.  Přejděte k poskytovateli služby DNS (Domain Name), který hostuje váš název domény.
2.  Přidejte záznam TXT pro vaši doménu, která používá hodnotu domény token, který se zobrazí na webu Azure Portal. 

Počkejte několik minut, než proběhne rozšíření DNS, spustit a pak vyberte **aktualizovat** tlačítko spusťte ověření. 

Jako alternativu můžete použít metodu webové stránce HTML ručně ověřit vaši doménu. Tato metoda umožňuje certifikační autoritě potvrdit vlastnictví domény, který certifikát je vydaný pro domény.

1.  Vytvořte soubor HTML, který je pojmenován {token pro ověření domény} .html. Obsah tohoto souboru by měla být hodnota tokenu pro ověření domény.
3.  Odešlete tento soubor v kořenové složce webového serveru, který je hostitelem vaší domény.
4.  Vyberte **aktualizovat** zkontrolovat stav certifikátu. Může trvat několik minut na dokončení ověření.

Například, pokud si kupujete standardní certifikát pro azure.com s token 1234abcd ověření domény, webové žádosti na https://azure.com/1234abcd.html by měla vrátit 1234abcd. 

> [!IMPORTANT]
> Pořadí certifikátů má pouze 15 dnů, dokončit operace ověření domény. Po 15 dnech certifikační autorita zamítne certifikát a se vám neúčtují poplatky pro certifikát. V takovém případě odstranit tento certifikát a zkuste to znovu.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Není možné koupit doménu

#### <a name="symptom"></a>Příznak
Nelze nakupovat domény z domény webové aplikace nebo služby App Service na webu Azure Portal.

#### <a name="cause-and-solution"></a>Příčina a řešení

K tomuto problému dochází z jednoho z následujících důvodů:

- Neexistuje žádná platební karta u předplatného Azure nebo platební karty je neplatné.

    **Řešení**: Ke svému předplatnému přidáte uvedenou platnou platební kartu.

- Nejste se vlastník předplatného, tak, že nemáte oprávnění k nákupu domény.

    **Řešení**: [Přiřazení role vlastníka](../role-based-access-control/role-assignments-portal.md) ke svému účtu. Nebo se obraťte na správce předplatného o získání oprávnění kupovat doménu.
- Dosáhli jste limitu pro nákup domén v rámci předplatného. Současný limit je 20.

    **Řešení**: Chcete-li požádat o zvýšení limitu, obraťte se na [podpory Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Typ vašeho předplatného Azure nepodporuje koupit doménu služby App Service.

    **Řešení**: Upgradujte vaše předplatné Azure na jiný typ předplatného, jako je například předplatné s průběžnými platbami.

### <a name="you-cant-add-a-host-name-to-a-web-app"></a>Nelze přidat název hostitele do webové aplikace 

#### <a name="symptom"></a>Příznak

Když přidáte název hostitele, proces selže a ověřit doménu ověřit.

#### <a name="cause"></a>Příčina 

K tomuto problému dochází z jednoho z následujících důvodů:

- Nemáte oprávnění přidat název hostitele.

    **Řešení**: Požádejte správce předplatného o udělení oprávnění přidat název hostitele.
- Nepovedlo se ověřit vaše vlastnictví domény.

    **Řešení**: Ověřte, zda je správně nakonfigurována vaše CNAME nebo záznam. K mapování vlastní domény na webovou aplikaci, vytvořte záznam CNAME nebo záznam. Pokud chcete použít kořenovou doménu, musíte použít záznamy A a TXT:

    |Typ záznamu|Hostitel|Přejděte na|
    |------|------|-----|
    |A|@|IP adresa pro webovou aplikaci|
    |TXT|@|< název aplikace >. azurewebsites.net|
    |CNAME|www|< název aplikace >. azurewebsites.net|

### <a name="dns-cant-be-resolved"></a>Nelze přeložit DNS

#### <a name="symptom"></a>Příznak

Dostanete se následující chybová zpráva:

"Záznam DNS nebyl nalezen."

#### <a name="cause"></a>Příčina
K tomuto problému dochází z jednoho z následujících důvodů:

- Time to live (TTL) období nevypršela platnost. Zkontrolujte konfiguraci DNS pro vaši doménu k určení hodnoty TTL a potom počkejte období vypršení platnosti.
- Konfigurace DNS je nesprávná.

#### <a name="solution"></a>Řešení
- Počkejte, až 48 hodin pro tento problém sám nevyřeší.
- Pokud nastavení TTL můžete změnit v konfiguraci DNS, změňte hodnotu na 5 minut, pokud chcete zobrazit, zda tento problém řeší.
- Použití [WhatsmyDNS.net](https://www.whatsmydns.net/) k ověření, že vaše doména odkazuje na IP adresu webové aplikace. Pokud ne, nakonfigurujte záznam na správnou IP adresu webové aplikace.

### <a name="you-need-to-restore-a-deleted-domain"></a>Je třeba obnovit Odstraněná doména 

#### <a name="symptom"></a>Příznak
Vaše doména už nejsou viditelné na webu Azure Portal.

#### <a name="cause"></a>Příčina 
Vlastníkem předplatného může-li neúmyslně domény.

#### <a name="solution"></a>Řešení
Pokud vaše doména byla odstraněna před méně než sedm dní, doménu ještě nezačala proces odstranění. V takovém případě můžete koupit stejné domény znovu na webu Azure portal v rámci stejného předplatného. (Nezapomeňte do vyhledávacího pole zadejte název domény přesné.) Vám nebude účtovat znovu pro tuto doménu. Pokud domény byl odstraněn před více než sedm dnů, obraťte se na [podpory Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) nápovědu k obnovení domény.

### <a name="a-custom-domain-returns-a-404-error"></a>Vlastní domény vrátí chybu 404 

#### <a name="symptom"></a>Příznak

Přejděte na web s použitím vlastního názvu domény zobrazí následující chybová zpráva:

"Chyba 404webovou aplikaci nebyl nalezen."


#### <a name="cause-and-solution"></a>Příčina a řešení

**Příčiny 1** 

Vlastní domény, který jste nakonfigurovali chybí záznam CNAME nebo A. 

**Řešení příčiny 1**

- Pokud jste přidali záznam A, ujistěte se, že je taky přidaný záznam TXT. Další informace najdete v tématu [vytvořte záznam a](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Pokud není nutné používat kořenové domény pro webovou aplikaci, doporučujeme použít záznam CNAME, který místo záznam.
- Nepoužívejte záznam CNAME i záznam stejné domény. To může způsobit konflikt a zabránit doméně Probíhá řešení. 

**Příčiny 2** 

Internetového prohlížeče může přesto být ukládání do mezipaměti starou IP adresu pro vaši doménu. 

**Řešení příčiny 2**

Zrušte v prohlížeči. Pro zařízení s Windows, můžete spustit příkaz `ipconfig /flushdns`. Použití [WhatsmyDNS.net](https://www.whatsmydns.net/) k ověření, že vaše doména odkazuje na IP adresu webové aplikace. 

### <a name="you-cant-add-a-subdomain"></a>Nelze přidat subdoménu 

#### <a name="symptom"></a>Příznak

Nový název hostitele nelze přidat do webové aplikace k přiřazení subdomény.

#### <a name="solution"></a>Řešení

- Obraťte se na správce předplatného, abyste měli jistotu, že máte oprávnění k přidání názvu hostitele do webové aplikace.
- Pokud potřebujete další subdomény, doporučujeme změnit hostování domény do Azure DNS. S využitím Azure DNS, můžete přidat hostitele 500 do vaší webové aplikace. Další informace najdete v tématu [přidání subdomény](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 


















