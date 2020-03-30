---
title: Poradce při potížích s certifikáty domény a SSL
description: Najděte řešení běžných problémů, se kterými se můžete setkat při konfiguraci domény nebo certifikátu SSL ve službě Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: e299821b54692327cbb7d497af0295e3b93658cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966980"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-app-service"></a>Poradce při potížích s doménou a certifikátem SSL ve službě Azure App Service

Tento článek uvádí běžné problémy, se kterými se můžete setkat při konfiguraci domény nebo certifikátu SSL pro vaše webové aplikace ve službě Azure App Service. Popisuje také možné příčiny a řešení těchto problémů.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Problémy s certifikátem

### <a name="you-cant-add-an-ssl-certificate-binding-to-an-app"></a>Do aplikace nelze přidat vazbu certifikátu SSL. 

#### <a name="symptom"></a>Příznak

Při přidání vazby SSL se zobrazí následující chybová zpráva:

"Přidání vazby SSL se nezdařilo. Certifikát nelze nastavit pro existující program VIP, protože tento certifikát již používá jiný program VIP."

#### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud máte více vazeb SSL založené na protokolu IP pro stejnou adresu IP ve více aplikacích. Například aplikace A má protokol SSL založený na protokolu IP se starým certifikátem. Aplikace B má protokol SSL založený na protokolu IP s novým certifikátem pro stejnou IP adresu. Když aktualizujete vazbu SSL aplikace s novým certifikátem, s touto chybou se nezdaří, protože se používá stejná IP adresa pro jinou aplikaci. 

#### <a name="solution"></a>Řešení 

Chcete-li tento problém vyřešit, použijte jednu z následujících metod:

- Odstraňte vazbu SSL založenou na protokolu IP pro aplikaci, která používá starý certifikát. 
- Vytvořte novou vazbu SSL založenou na protokolu IP, která používá nový certifikát.

### <a name="you-cant-delete-a-certificate"></a>Certifikát nelze odstranit. 

#### <a name="symptom"></a>Příznak

Při pokusu o odstranění certifikátu se zobrazí následující chybová zpráva:

"Certifikát nelze odstranit, protože je aktuálně používán ve vazbě SSL. Vazba SSL musí být před odstraněním certifikátu odebrána."

#### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud jiný nástroj používá certifikát.

#### <a name="solution"></a>Řešení

Odeberte vazbu SSL pro tento certifikát z aplikací. Potom zkuste odstranit certifikát. Pokud se vám certifikát pořád nepodaří odstranit, vymažte mezipaměť internetového prohlížeče a znovu otevřete portál Azure v novém okně prohlížeče. Potom zkuste odstranit certifikát.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Certifikát služby App Service nelze zakoupit 

#### <a name="symptom"></a>Příznak
[Certifikát služby Azure App Service](./configure-ssl-certificate.md#import-an-app-service-certificate) nelze zakoupit na webu Azure Portal.

#### <a name="cause-and-solution"></a>Příčina a řešení
K tomuto problému může dojít z některého z následujících důvodů:

- Plán služby App Service je zdarma nebo sdílený. Tyto cenové úrovně nepodporují SSL. 

    **Řešení:** Upgradujte plán služby App Service pro aplikaci na standardní.

- Předplatné nemá platnou platební kartu.

    **Řešení:** Přidejte k předplatnému platnou platební kartu. 

- Nabídka předplatného nepodporuje nákup certifikátu služby App Service, jako je Microsoft Student.  

    **Řešení:** Upgradujte předplatné. 

- Předplatné dosáhlo limitu nákupů, které jsou povoleny pro předplatné.

    **Řešení:** Certifikáty služby App Service mají limit 10 nákupů certifikátů pro typy předplatného průběžných plateb a EA. U jiných typů předplatného je limit 3. Chcete-li zvýšit limit, obraťte se na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Certifikát služby App Service byl označen jako podvod. Zobrazila se následující chybová zpráva: "Váš certifikát byl označen za možný podvod. Žádost je v současné době přezkoumávána. Pokud se certifikát nestane použitelným do 24 hodin, obraťte se na podporu Azure."

    **Řešení**: Pokud je certifikát označen jako podvod a není vyřešen po 24 hodinách, postupujte takto:

    1. Přihlaste se k [portálu Azure](https://portal.azure.com).
    2. Přejděte na **Certifikáty služby App Service**a vyberte certifikát.
    3. Vyberte **krok konfigurace certifikátu** > **2: Ověření** > **domény**. Tento krok odešle e-mailové oznámení poskytovateli certifikátu Azure k vyřešení problému.

## <a name="custom-domain-problems"></a>Problémy s vlastní doménou

### <a name="a-custom-domain-returns-a-404-error"></a>Vlastní doména vrátí chybu 404. 

#### <a name="symptom"></a>Příznak

Při procházení webu pomocí vlastního názvu domény se zobrazí následující chybová zpráva:

"Chyba 404-Web ová aplikace nebyla nalezena."

#### <a name="cause-and-solution"></a>Příčina a řešení

**Příčina 1** 

Ve vlastní doméně, kterou jste nakonfigurovali, chybí záznam CNAME nebo A. 

**Řešení 1. příčiny**

- Pokud jste přidali záznam A, ujistěte se, že je přidán také záznam TXT. Další informace naleznete [v tématu Vytvoření záznamu A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Pokud pro svou aplikaci nemusíte používat kořenovou doménu, doporučujeme použít místo záznamu A záznam CNAME.
- Nepoužívejte záznam CNAME i záznam A pro stejnou doménu. Tento problém může způsobit konflikt a zabránit řešení domény. 

**Příčina 2** 

Internetový prohlížeč může stále uvázat do mezipaměti starou IP adresu pro vaši doménu. 

**Řešení pro příčinu 2**

Vymažte prohlížeč. Pro zařízení se systémem Windows `ipconfig /flushdns`můžete spustit příkaz . Pomocí [WhatsmyDNS.net](https://www.whatsmydns.net/) ověřte, zda vaše doména odkazuje na IP adresu aplikace. 

### <a name="you-cant-add-a-subdomain"></a>Nelze přidat subdoménu 

#### <a name="symptom"></a>Příznak

Do aplikace nemůžete přidat nový název hostitele a přiřadit subdoménu.

#### <a name="solution"></a>Řešení

- Obraťte se na správce předplatného a ujistěte se, že máte oprávnění k přidání názvu hostitele do aplikace.
- Pokud potřebujete další subdomény, doporučujeme změnit hostování domény na Azure Domain Name Service (DNS). Pomocí Azure DNS můžete do aplikace přidat 500 názvů hostitelů. Další informace naleznete [v tématu Přidání subdomény](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).

### <a name="dns-cant-be-resolved"></a>DNS nelze přeložit

#### <a name="symptom"></a>Příznak

Zobrazila se následující chybová zpráva:

"Záznam DNS nelze nalézt."

#### <a name="cause"></a>Příčina
K tomuto problému dochází z jednoho z následujících důvodů:

- Doba života (TTL) neuplynula. Zkontrolujte konfiguraci DNS pro vaši doménu, abyste určili hodnotu TTL, a počkejte, až vyprší platnost období.
- Konfigurace DNS je nesprávná.

#### <a name="solution"></a>Řešení
- Počkejte 48 hodin, než se tento problém vyřeší sám.
- Pokud můžete změnit nastavení TTL v konfiguraci DNS, změňte hodnotu na 5 minut, abyste zjistili, zda se tím problém vyřeší.
- Pomocí [WhatsmyDNS.net](https://www.whatsmydns.net/) ověřte, zda vaše doména odkazuje na IP adresu aplikace. Pokud tomu tak není, nakonfigurujte záznam A na správnou IP adresu aplikace.

### <a name="you-need-to-restore-a-deleted-domain"></a>Je třeba obnovit odstraněnou doménu 

#### <a name="symptom"></a>Příznak
Vaše doména už není viditelná na webu Azure Portal.

#### <a name="cause"></a>Příčina 
Vlastník předplatného pravděpodobně omylem odstranil doménu.

#### <a name="solution"></a>Řešení
Pokud byla vaše doména odstraněna před méně než sedmi dny, doména ještě nezahájila proces odstranění. V takovém případě můžete znovu koupit stejnou doménu na webu Azure Portal v rámci stejného předplatného. (Do vyhledávacího pole zadejte přesný název domény.) Za tuto doménu vám nebudou znovu účtovány poplatky. Pokud byla doména odstraněna před více než sedmi dny, obraťte se na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o pomoc s obnovením domény.

## <a name="domain-problems"></a>Problémy s doménou

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Zakoupili jste certifikát SSL pro nesprávnou doménu

#### <a name="symptom"></a>Příznak

Zakoupili jste certifikát služby App Service pro nesprávnou doménu. Certifikát nelze aktualizovat tak, aby používal správnou doménu.

#### <a name="solution"></a>Řešení

Odstraňte tento certifikát a zakupte nový certifikát.

Pokud je aktuální certifikát, který používá nesprávnou doménu, ve stavu Vystaveno, bude vám tento certifikát účtován také. Certifikáty služby App Service nejsou vratné, ale můžete kontaktovat [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a zjistit, jestli existují další možnosti. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Certifikát služby App Service byl obnoven, ale aplikace zobrazuje starý certifikát 

#### <a name="symptom"></a>Příznak

Certifikát služby App Service byl obnoven, ale aplikace, která používá certifikát služby App Service, stále používá starý certifikát. Také jste obdrželi upozornění, že je vyžadován protokol HTTPS.

#### <a name="cause"></a>Příčina 
Služba App Service automaticky synchronizuje váš certifikát do 48 hodin. Při otočení nebo aktualizaci certifikátu někdy aplikace stále načítá starý certifikát a nikoli nově aktualizovaný certifikát. Důvodem je, že úloha synchronizace prostředku certifikátu ještě nebyla spuštěna. Klikněte na Synchronizovat. Operace synchronizace automaticky aktualizuje vazby názvu hostitele pro certifikát ve službě App Service, aniž by došlo k prostojům vašich aplikací.
 
#### <a name="solution"></a>Řešení

Synchronizaci certifikátu můžete vynutit:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **Certifikáty služby App Service**a pak vyberte certifikát.
2. Vyberte **Znovu zakódit a synchronizovat**a pak vyberte **Synchronizovat**. Synchronizace trvá nějakou dobu. 
3. Po dokončení synchronizace se zobrazí následující oznámení: "Úspěšně aktualizovány všechny prostředky s nejnovější medaienci."

### <a name="domain-verification-is-not-working"></a>Ověření domény nefunguje 

#### <a name="symptom"></a>Příznak 
Certifikát služby App Service vyžaduje ověření domény, než bude certifikát připraven k použití. Vyberete-li **možnost Ověřit**, proces se nezdaří.

#### <a name="solution"></a>Řešení
Ručně ověřte svou doménu přidáním záznamu TXT:
 
1.  Přejděte na poskytovatele dns (Domain Name Service), který je hostitelem vašeho názvu domény.
2.  Přidejte txt záznam pro vaši doménu, která používá hodnotu tokenu domény, který je zobrazen na webu Azure Portal. 

Počkejte několik minut, než se spustí šíření DNS, a pak vyberte tlačítko **Aktualizovat,** které aktivuje ověření. 

Jako alternativu můžete použít metodu webové stránky HTML k ručnímu ověření domény. Tato metoda umožňuje certifikační autoritě potvrdit vlastnictví domény, pro kterou je certifikát vydán.

1.  Vytvořte soubor HTML s názvem {domain verification token}.html. Obsah tohoto souboru by měla být hodnota tokenu ověření domény.
3.  Nahrajte tento soubor v kořenovém adresáři webového serveru, který je hostitelem vaší domény.
4.  Chcete-li zkontrolovat stav certifikátu, vyberte **možnost Aktualizovat.** Dokončení ověření může trvat několik minut.

Například pokud kupujete standardní certifikát pro azure.com s tokenem pro ověření domény 1234abcd, webový požadavek na https://azure.com/1234abcd.html by měl vrátit 1234abcd. 

> [!IMPORTANT]
> Objednávka certifikátu má pouze 15 dní na dokončení operace ověření domény. Po 15 dnech certifikační autorita certifikát odmítne a certifikát se vám neúčtuje. V takovém případě odstraňte tento certifikát a akci opakujte.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Doménu nelze zakoupit

#### <a name="symptom"></a>Příznak
Doménu služby App Service si na webu Azure Portal nemůžete koupit.

#### <a name="cause-and-solution"></a>Příčina a řešení

K tomuto problému dochází z jednoho z následujících důvodů:

- V předplatném Azure není žádná platební karta, případně je platební karta neplatná.

    **Řešení:** Přidejte k předplatnému platnou platební kartu.

- Nejste vlastníkem předplatného, takže nemáte oprávnění k nákupu domény.

    **Řešení:** [Přiřazení role vlastníka](../role-based-access-control/role-assignments-portal.md) ke svému účtu. Nebo se obraťte na správce předplatného získat oprávnění k nákupu domény.
- Dosáhli jste limitu pro nákup domén pro vaše předplatné. Aktuální limit je 20.

    **Řešení:** Chcete-li požádat o zvýšení limitu, obraťte se na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Váš typ předplatného Azure nepodporuje nákup domény služby App Service.

    **Řešení:** Upgradujte předplatné Azure na jiný typ předplatného, jako je například předplatné s průběžným platbou.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Do aplikace nelze přidat název hostitele. 

#### <a name="symptom"></a>Příznak

Přidáte-li název hostitele, proces se nezdaří ověřit a ověřit doménu.

#### <a name="cause"></a>Příčina 

K tomuto problému dochází z jednoho z následujících důvodů:

- Nemáte oprávnění k přidání názvu hostitele.

    **Řešení**: Požádejte správce předplatného, aby vám dal oprávnění k přidání názvu hostitele.
- Vlastnictví domény nelze ověřit.

    **Řešení:** Ověřte, zda je záznam CNAME nebo A správně nakonfigurován. Chcete-li namapovat vlastní doménu na aplikaci, vytvořte záznam CNAME nebo záznam A. Pokud chcete použít kořenovou doménu, musíte použít záznamy A a TXT:

    |Typ záznamu|Hostitel|Přejděte na|
    |------|------|-----|
    |A|@|IP adresa aplikace|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Nejčastější dotazy

**Musím si po zakoupení nakonfigurovat vlastní doménu pro svůj web?**

Když si koupíte doménu z portálu Azure, aplikace App Service se automaticky nakonfiguruje na použití této vlastní domény. Nemusíte podnikat žádné další kroky. Další informace našlápnout na [svépomoc služby Azure App Service: Přidání vlastního názvu domény](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) na kanálu 9.

**Můžu místo toho použít doménu zakoupenou na webu Azure Portal k překážce na virtuální počítač Azure?**

Ano, můžete nasměrovat doménu na virtuální hod. Další informace najdete v tématu popisujícím [použití Azure DNS k určení nastavení vlastní domény pro službu Azure](../dns/dns-custom-domain.md).

**Je moje doména hostovaná GoDaddy nebo Azure DNS?**

Domény služby App Service používají K hostování domén GoDaddy a Azure DNS. 

**Mám povoleno automatické obnovení, ale přesto jsem obdržel oznámení o obnovení mé domény prostřednictvím e-mailu. Co mám dělat?**

Pokud máte povoleno automatické prodlužování, není nutné provádět žádnou akci. E-mail s oznámením je k dispozici s oznámením, že doména má blízko k vypršení platnosti, a ručně se obnovovat, pokud není povoleno automatické prodlužování.

**Bude se mi účtovat Azure DNS hostující svou doménu?**

Počáteční náklady na nákup domény se vztahují pouze na registraci domény. Kromě nákladů na registraci se účtují poplatky za Azure DNS na základě vašeho využití. Další informace najdete v tématu [Azure DNS ceny pro](https://azure.microsoft.com/pricing/details/dns/) další podrobnosti.

**Svou doménu jsem si koupil dříve z webu Azure Portal a chci přejít z hostování GoDaddy na Azure DNS hosting. Jak to mám udělat?**

Není povinné migrovat na Azure DNS hosting. Pokud chcete migrovat na Azure DNS, prostředí správy domén na webu Azure Portal o poskytuje informace o krocích nezbytných k přechodu na Azure DNS. Pokud byla doména zakoupena prostřednictvím služby App Service, migrace z hostování GoDaddy na Azure DNS je relativně bezproblémová procedura.

**Chci si koupit svou doménu z domény Služby App Service, ale můžu svou doménu hostovat na GoDaddy místo Azure DNS?**

července 2017 jsou domény služby App Service zakoupené na portálu hostované na Azure DNS. Pokud dáváte přednost použití jiného poskytovatele hostingu, musíte jít na jejich webové stránky, abyste získali řešení hostingu domény.

**Musím platit za ochranu soukromí pro svou doménu?**

Když si koupíte doménu prostřednictvím portálu Azure, můžete přidat ochranu osobních údajů bez dalších nákladů. To je jedna z výhod nákupu domény prostřednictvím služby Azure App Service.

**Pokud se rozhodnu, že už nechci svou doménu, mohu dostat své peníze zpět?**

Při nákupu domény vám nebude účtováno po dobu pěti dnů, během které se můžete rozhodnout, že doménu nechcete. Pokud se rozhodnete, že doménu nechcete v tomto pětidenním období, nebudou vám účtovány poplatky. (.uk domény jsou výjimkou z tohoto. Pokud si zakoupíte doménu .uk, bude vám okamžitě účtovánpoplatek a nelze vám vrátit peníze.)

**Můžu v předplatném použít doménu v jiné aplikaci Azure App Service?**

Ano. Při přístupu k vlastní domény a SSL okno na webu Azure portal, uvidíte domény, které jste si koupili. Aplikaci můžete nakonfigurovat tak, aby používala některou z těchto domén.

**Můžu převést doménu z jednoho předplatného na jiné předplatné?**

Doménu můžete přesunout do jiné skupiny předplatného nebo prostředků pomocí rutiny [Move-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) PowerShell.

**Jak můžu spravovat svou vlastní doménu, když momentálně nemám aplikaci Azure App Service?**

Doménu můžete spravovat i v případě, že webovou aplikaci služby App Service nemáte. Doménu lze použít pro služby Azure, jako je virtuální počítač, úložiště atd. Pokud máte v úmyslu použít doménu pro webové aplikace App Service, musíte zahrnout webovou aplikaci, která není v plánu Služby bezplatných aplikací, abyste mohli doménu svázat s vaší webovou aplikací.

**Můžu přesunout webovou aplikaci s vlastní doménou do jiného předplatného nebo z prostředí App Service Environment v1 na V2?**

Ano, webovou aplikaci můžete přesouvat mezi předplatnými. Postupujte podle pokynů v [části Jak přesunout prostředky v Azure](../azure-resource-manager/management/move-resource-group-and-subscription.md). Při přesouvání webové aplikace existuje několik omezení. Další informace naleznete [v tématu Omezení pro přesun prostředků služby App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

Po přesunutí webové aplikace by měly zůstat stejné vazby názvů hostitelů domén v rámci nastavení vlastních domén. Ke konfiguraci vazeb názvů hostitelů nejsou nutné žádné další kroky.
