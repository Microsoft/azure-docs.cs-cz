---
title: Řešení potíží s certifikáty domén a TLS/SSL
description: Vyhledá řešení běžných problémů, se kterými se můžete setkat při konfiguraci certifikátu domény nebo TLS/SSL v Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 691cbd79e82432c8e919dcbb51642a76000296dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97607605"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Řešení potíží s certifikátem v doméně a TLS/SSL v Azure App Service

V tomto článku jsou uvedené běžné problémy, se kterými se můžete setkat při konfiguraci certifikátu domény nebo TLS/SSL pro webové aplikace v Azure App Service. Popisuje také možné příčiny a řešení těchto problémů.

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete se obrátit na odborníky na Azure na [fórech MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Problémy s certifikátem

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>Do aplikace nelze přidat vazbu certifikátu TLS/SSL. 

#### <a name="symptom"></a>Příznak

Při přidávání vazby TLS se zobrazí následující chybová zpráva:

"Nepovedlo se přidat vazbu SSL. Nejde nastavit certifikát pro existující virtuální IP adresu, protože tento certifikát už používá jiná virtuální IP adresa.

#### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud máte více vazeb SSL založených na protokolu IP pro stejnou IP adresu napříč více aplikacemi. Například aplikace A obsahuje protokol SSL založený na protokolu IP se starým certifikátem. Aplikace B obsahuje protokol SSL založený na protokolu IP s novým certifikátem pro stejnou IP adresu. Když aktualizujete vazbu TLS aplikace pomocí nového certifikátu, dojde k chybě s touto chybou, protože se stejná IP adresa používá pro jinou aplikaci. 

#### <a name="solution"></a>Řešení 

Chcete-li tento problém vyřešit, použijte jednu z následujících metod:

- Odstraňte vazbu SSL založenou na protokolu IP v aplikaci, která používá starý certifikát. 
- Vytvořte novou vazbu SSL založenou na protokolu IP, která používá nový certifikát.

### <a name="you-cant-delete-a-certificate"></a>Certifikát nemůžete odstranit. 

#### <a name="symptom"></a>Příznak

Při pokusu o odstranění certifikátu se zobrazí následující chybová zpráva:

"Certifikát nelze odstranit, protože je aktuálně používán ve vazbě TLS/SSL. Než budete moct certifikát odstranit, musí se odebrat vazba TLS.

#### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud jiná aplikace používá certifikát.

#### <a name="solution"></a>Řešení

Odeberte z aplikací vazbu TLS pro tento certifikát. Pak zkuste certifikát odstranit. Pokud certifikát stále nemůžete odstranit, vymažte mezipaměť internetového prohlížeče a znovu otevřete Azure Portal v novém okně prohlížeče. Pak zkuste certifikát odstranit.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Nemůžete si koupit certifikát App Service. 

#### <a name="symptom"></a>Příznak
Z Azure Portal si nemůžete koupit [certifikát Azure App Service](./configure-ssl-certificate.md#import-an-app-service-certificate) .

#### <a name="cause-and-solution"></a>Příčina a řešení
K tomuto problému může dojít z některého z následujících důvodů:

- Plán App Service je bezplatný nebo sdílený. Tyto cenové úrovně nepodporují protokol TLS. 

    **Řešení**: upgradujte App Service plán pro aplikaci na standard.

- Předplatné nemá platnou platební kartu.

    **Řešení**: přidejte do svého předplatného platnou platební kartu. 

- Nabídka předplatného nepodporuje nákup certifikátu App Service, jako je například Microsoft student.  

    **Řešení**: Upgradujte své předplatné. 

- Předplatné dosáhlo limitu nákupů, které jsou povoleny v rámci předplatného.

    **Řešení**: App Service certifikáty mají omezení 10 nákupů certifikátů pro typy předplatného s průběžnými platbami a EA. Pro jiné typy předplatného je limit 3. Pokud chcete tento limit zvýšit, kontaktujte [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Certifikát App Service byl označen jako podvod. Zobrazila se následující chybová zpráva: "váš certifikát byl označen příznakem pro případné podvody. V tuto chvíli je žádost v současnosti pod kontrolou. Pokud se certifikát nestane použitelným během 24 hodin, obraťte se na podporu Azure. "

    **Řešení**: Pokud je certifikát označený jako podvod a za 24 hodin se nevyřešil, postupujte podle těchto kroků:

    1. Přihlaste se na [Azure Portal](https://portal.azure.com).
    2. Přejít na **App Service certifikáty** a vyberte certifikát.
    3. Vyberte **Konfigurace certifikátu**  >  **Krok 2: ověření**  >  **domény ověření**. Tento krok pošle e-mailové oznámení poskytovateli certifikátů Azure, aby problém vyřešil.

## <a name="custom-domain-problems"></a>Problémy s vlastní doménou

### <a name="a-custom-domain-returns-a-404-error"></a>Vlastní doména vrátí chybu 404. 

#### <a name="symptom"></a>Příznak

Když přejdete na web pomocí vlastního názvu domény, zobrazí se následující chybová zpráva:

Chyba 404: webová aplikace nebyla nalezena.

#### <a name="cause-and-solution"></a>Příčina a řešení

**Příčina 1** 

V vlastní doméně, kterou jste nakonfigurovali, chybí záznam CNAME nebo záznam. 

**Řešení 1. příčiny**

- Pokud jste přidali záznam A, ujistěte se, že je přidán také záznam TXT. Další informace najdete v tématu [Vytvoření záznamu A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Pokud pro vaši aplikaci nemusíte používat kořenovou doménu, doporučujeme místo záznamu A použít záznam CNAME.
- Nepoužívejte záznam CNAME a záznam a pro stejnou doménu. Tento problém může způsobit konflikt a zabránit tomu, aby se doména vyřešila. 

**Příčina 2** 

Internetový prohlížeč možná pořád ukládá do mezipaměti starou IP adresu pro vaši doménu. 

**Řešení pro příčinu 2**

Vymažte prohlížeč. Pro zařízení s Windows můžete spustit příkaz `ipconfig /flushdns` . Pomocí [WhatsmyDNS.NET](https://www.whatsmydns.net/) ověřte, že vaše doména odkazuje na IP adresu aplikace.

### <a name="you-cant-add-a-subdomain"></a>Nejde přidat subdoménu. 

#### <a name="symptom"></a>Příznak

Do aplikace nelze přidat nový název hostitele, aby bylo možné přiřadit subdoménu.

#### <a name="solution"></a>Řešení

- Obraťte se na správce předplatného a ujistěte se, že máte oprávnění k přidání názvu hostitele do aplikace.
- Pokud potřebujete více subdomén, doporučujeme změnit hostování domény ve službě Azure Domain Name Service (DNS). Pomocí Azure DNS můžete do aplikace přidat názvy hostitelů 500. Další informace najdete v tématu [Přidání subdomény](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website).

### <a name="dns-cant-be-resolved"></a>DNS se nedá přeložit.

#### <a name="symptom"></a>Příznak

Zobrazila se tato chybová zpráva:

"Záznam DNS se nepovedlo najít."

#### <a name="cause"></a>Příčina
K tomuto problému dochází z jednoho z následujících důvodů:

- Doba TTL (Time to Live) nevypršela. Zkontrolujte konfiguraci DNS pro vaši doménu a určete hodnotu TTL a potom počkejte na vypršení platnosti období.
- Konfigurace DNS je nesprávná.

#### <a name="solution"></a>Řešení
- Počkejte na 48 hodin, než se tento problém vyřeší.
- Pokud v konfiguraci DNS můžete změnit nastavení TTL, změňte hodnotu na 5 minut, aby se zobrazilo, jestli se problém vyřeší.
- Pomocí [WhatsmyDNS.NET](https://www.whatsmydns.net/) ověřte, že vaše doména odkazuje na IP adresu aplikace. Pokud tomu tak není, nakonfigurujte záznam A na správnou IP adresu aplikace.

### <a name="you-need-to-restore-a-deleted-domain"></a>Musíte obnovit odstraněnou doménu. 

#### <a name="symptom"></a>Příznak
Vaše doména již není v Azure Portal viditelná.

#### <a name="cause"></a>Příčina 
Vlastník předplatného může doménu omylem odstranit.

#### <a name="solution"></a>Řešení
Pokud se vaše doména odstranila před méně než sedmi dny, doména ještě nezačala proces odstranění. V takovém případě můžete stejnou doménu koupit znovu na Azure Portal v rámci stejného předplatného. (Nezapomeňte do vyhledávacího pole zadat přesný název domény.) Pro tuto doménu se vám nebude nic účtovat. Pokud se doména odstranila před více než sedmi dny, obraťte se na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a požádejte o pomoc s obnovením domény.

## <a name="domain-problems"></a>Problémy s doménou

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>Zakoupili jste certifikát TLS/SSL pro špatnou doménu.

#### <a name="symptom"></a>Příznak

Zakoupili jste certifikát App Service pro špatnou doménu. Certifikát nemůžete aktualizovat, aby používal správnou doménu.

#### <a name="solution"></a>Řešení

Odstraňte tento certifikát a potom Kupte nový certifikát.

Pokud je aktuální certifikát, který používá špatnou doménu, ve stavu vydáno, bude se vám také účtovat tento certifikát. Certifikáty App Service nelze znovu financovat, ale můžete kontaktovat [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a zjistit, zda jsou k dispozici jiné možnosti. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Certifikát App Service se obnovil, ale aplikace zobrazuje starý certifikát. 

#### <a name="symptom"></a>Příznak

Certifikát App Service se obnovil, ale aplikace, která používá certifikát App Service, pořád používá starý certifikát. Také jste obdrželi upozornění, že je vyžadován protokol HTTPS.

#### <a name="cause"></a>Příčina 
App Service automaticky synchronizuje certifikát během 48 hodin. Při otočení nebo aktualizaci certifikátu někdy aplikace stále načítá starý certifikát a nikoli nově aktualizovaný certifikát. Důvodem je, že úloha synchronizace prostředku certifikátu ještě neběžela. Klikněte na synchronizovat. Operace synchronizace automaticky aktualizuje vazby hostitelů pro certifikát v App Service, aniž by to mělo za následek jakékoliv výpadky aplikací.

#### <a name="solution"></a>Řešení

Můžete vynutit synchronizaci certifikátu:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **App Service certifikáty** a pak vyberte certifikát.
2. Vyberte opětovné vytvoření klíče **a synchronizaci** a pak vyberte **synchronizovat**. Dokončení synchronizace trvá déle. 
3. Po dokončení synchronizace se zobrazí následující oznámení: "úspěšně se aktualizovaly všechny prostředky s nejnovějším certifikátem".

### <a name="domain-verification-is-not-working"></a>Ověřování domény nefunguje 

#### <a name="symptom"></a>Příznak 
App Service certifikát vyžaduje ověření domény před tím, než bude certifikát připravený k použití. Když vyberete **ověřit**, proces se nezdařil.

#### <a name="solution"></a>Řešení
Ručně ověřte doménu přidáním záznamu TXT:

1. Přejděte k poskytovateli DNS (Domain Name System), který je hostitelem vašeho názvu domény.
1. Přidejte pro vaši doménu záznam TXT s hodnotou tokenu domény, který se zobrazuje na webu Azure Portal. 

Počkejte několik minut, než se rozšíření DNS spustí, a potom výběrem tlačítka **aktualizovat** spusťte ověření. 

Jako alternativu můžete použít metodu webové stránky HTML k ručnímu ověření vaší domény. Tato metoda umožňuje certifikační autoritě potvrdit vlastnictví domény, pro kterou je certifikát vystavený.

1. Vytvořte soubor HTML s názvem {Domain Verification token}. html. Obsah tohoto souboru by měl být hodnota tokenu pro ověření domény.
1. Tento soubor nahrajte do kořenového adresáře webového serveru, který je hostitelem vaší domény.
1. Vyberte **aktualizovat** a ověřte stav certifikátu. Dokončení ověření může trvat několik minut.

Pokud například koupíte standardní certifikát pro azure.com s tokenem pro ověření domény 1234abcd, https://azure.com/1234abcd.html měla by webová žádost vrátit 1234abcd. 

> [!IMPORTANT]
> Dokončení operace ověření domény pro pořadí certifikátů má pouze 15 dní. Po 15 dnech certifikát odepře certifikační autorita a za certifikát se vám neúčtují poplatky. V takové situaci odstraňte tento certifikát a zkuste to znovu.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Nemůžete si koupit doménu.

#### <a name="symptom"></a>Příznak
V Azure Portal nelze koupit doménu App Service.

#### <a name="cause-and-solution"></a>Příčina a řešení

K tomuto problému dochází z jednoho z následujících důvodů:

- V předplatném Azure není žádná platební karta, případně je platební karta neplatná.

    **Řešení**: přidejte do svého předplatného platnou platební kartu.

- Nejste vlastníkem předplatného, takže nemáte oprávnění k nákupu domény.

    **Řešení**: [přiřaďte](../role-based-access-control/role-assignments-portal.md) k vašemu účtu roli vlastníka. Nebo se obraťte na správce předplatného, aby získal oprávnění k nákupu domény.
- Dosáhli jste limitu pro nákup domén pro vaše předplatné. Aktuální limit je 20.

    **Řešení**: Pokud chcete požádat o zvýšení limitu, obraťte se na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Váš typ předplatného Azure nepodporuje nákup domény služby App Service.

    **Řešení**: Upgradujte předplatné Azure na jiný typ předplatného, například předplatné s průběžnými platbami.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Do aplikace se nedá přidat název hostitele. 

#### <a name="symptom"></a>Příznak

Když přidáte název hostitele, proces nebude moci ověřit a ověřit doménu.

#### <a name="cause"></a>Příčina 

K tomuto problému dochází z jednoho z následujících důvodů:

- Nemáte oprávnění přidat název hostitele.

    **Řešení**: požádejte správce předplatného, aby vám udělil oprávnění k přidání názvu hostitele.
- Nebylo možné ověřit vlastnictví domény.

    **Řešení**: Ověřte, že je záznam CNAME nebo a správně nakonfigurován. K namapování vlastní domény na aplikaci vytvořte záznam CNAME nebo záznam A. Pokud chcete použít kořenovou doménu, je nutné použít záznamy a. TXT:

    |Typ záznamu|Hostitel|Ukázat na|
    |------|------|-----|
    |A|@|IP adresa pro aplikaci|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Časté otázky

**Musím po nákupu nakonfigurovat vlastní doménu pro svůj web?**

Když si koupíte doménu z Azure Portal, App Service aplikace se automaticky nakonfiguruje tak, aby používala tuto vlastní doménu. Nemusíte provádět žádné další kroky. Další informace najdete v [Azure App Service vlastní nápovědu: Přidání vlastního názvu domény](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) v channel9.

**Můžu místo toho použít doménu zakoupenou v Azure Portal, aby odkazovala na virtuální počítač Azure?**

Ano, doménu můžete nasměrovat na virtuální počítač. Další informace najdete v tématu popisujícím [použití Azure DNS k určení nastavení vlastní domény pro službu Azure](../dns/dns-custom-domain.md).

**Hostuje moje doména GoDaddy nebo Azure DNS?**

App Service domény používají GoDaddy k registraci domény a Azure DNS k hostování domén. 

**Bylo povoleno automatické obnovení, ale v e-mailu se stále dostalo oznámení o obnovení pro moji doménu. Co mám dělat?**

Pokud jste povolili automatické obnovení, nemusíte provádět žádnou akci. E-mail s oznámením vám poskytne informace o tom, že se doména blíží k vypršení platnosti a že se má obnovit ručně, pokud není povolená Automatická obnova.

**Bude se mi účtovat Azure DNS hostování naší domény?**

Počáteční náklady na nákup domény platí jenom pro registraci domény. Kromě nákladů na registraci se za Azure DNS účtují poplatky na základě vašeho využití. Další informace najdete v tématu [Azure DNS ceny](https://azure.microsoft.com/pricing/details/dns/) pro další podrobnosti.

**Koupil (a) jsem doménu dřív z Azure Portal a chtěli byste přejít z hostování GoDaddy do Azure DNS hostování. Jak to můžu udělat?**

Migrace na Azure DNS hostování není nutná. Pokud chcete migrovat na Azure DNS, najdete informace o krocích potřebných k přechodu do Azure DNS v části Azure Portal o prostředí správy domén v o nástroji. Pokud byla doména zakoupena prostřednictvím App Service, migrace z hostování GoDaddy do Azure DNS je poměrně bezproblémová procedura.

**Chtěl bych koupit moji doménu z App Service domény, ale můžu ji hostovat na GoDaddy místo Azure DNS?**

Od 24. července 2017 se App Service domény zakoupené na portálu hostují na Azure DNS. Pokud dáváte přednost jinému poskytovateli hostingu, musíte přejít na svůj web a získat řešení pro hostování domén.

**Musím pro svou doménu platit ochranu osobních údajů?**

Při nákupu domény prostřednictvím Azure Portal můžete zvolit, že chcete přidat ochranu osobních údajů bez dalších nákladů. Toto je jedna z výhod nákupu vaší domény prostřednictvím Azure App Service.

**Pokud se rozhodnu, že už nechci moje doména, můžu mi získat peníze zpátky?**

Když si koupíte doménu, nebudete se vám účtovat po dobu pěti dnů, během které se můžete rozhodnout, že tuto doménu nechcete. Pokud se rozhodnete, že nechcete mít doménu v rámci tohoto pětiletého období, nebudeme vám nic účtovat. (domény. Spojené království představují výjimku. Pokud si koupíte doménu. UK, účtují se okamžitě a vy nebudete moci být znovu zaplaceni.)

**Můžu v předplatném použít doménu v jiné aplikaci Azure App Service?**

Ano. Při přístupu k oknu vlastní domény a TLS v Azure Portal uvidíte domény, které jste zakoupili. Aplikaci můžete nakonfigurovat tak, aby používala některou z těchto domén.

**Můžu přenést doménu z jednoho předplatného do jiného předplatného?**

Doménu můžete přesunout do jiného předplatného nebo skupiny prostředků pomocí rutiny [Move-AzResource](/powershell/module/az.Resources/Move-azResource) prostředí PowerShell.

**Jak můžu spravovat vlastní doménu, pokud teď nemám aplikaci Azure App Service?**

Svoji doménu můžete spravovat i v případě, že nemáte App Service webovou aplikaci. Doména se dá použít pro služby Azure, jako je virtuální počítač, úložiště atd. Pokud máte v úmyslu použít doménu pro App Service Web Apps, musíte zahrnout webovou aplikaci, která není v plánu Free App Service, aby se dala vytvořit vazba domény k webové aplikaci.

**Můžu přesunout webovou aplikaci s vlastní doménou do jiného předplatného nebo z App Service Environment V1 na v2?**

Ano, můžete přesunout webovou aplikaci napříč předplatnými. Postupujte podle pokynů v tématu [Postup přesunutí prostředků v Azure](../azure-resource-manager/management/move-resource-group-and-subscription.md). Při přesunu webové aplikace existuje několik omezení. Další informace najdete v tématu [omezení pro přesunutí prostředků App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

Po přesunutí webové aplikace by vazby názvu hostitele domén v rámci nastavení vlastní domény měly zůstat stejné. Pro konfiguraci vazeb názvů hostitelů nejsou nutné žádné další kroky.