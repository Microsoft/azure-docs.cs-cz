---
title: Integrace služby Azure Key Vault se službou Azure Policy
description: Informace o tom, jak integrovat Azure Key Vault s Azure Policy
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 10/15/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 6ac4d0e0744bfc82a686671234e013b2dd717146
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92927749"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrace služby Azure Key Vault se službou Azure Policy

[Azure Policy](../../governance/policy/index.yml) je nástroj zásad správného řízení, který uživatelům poskytuje možnost provádět audit a správu prostředí Azure ve velkém měřítku. Azure Policy poskytuje možnost umístit guardrails do prostředků Azure, aby bylo zajištěno, že budou kompatibilní s přiřazenými pravidly zásad. Umožňuje uživatelům provádět audit, vynucování v reálném čase a nápravu jejich prostředí Azure. Výsledky auditů provedené zásadami budou uživatelům k dispozici na řídicím panelu dodržování předpisů, kde budou moci zobrazit podrobné informace o tom, které prostředky a součásti vyhovují a které ne.  Další informace najdete v tématu [Přehled služby Azure Policy](../../governance/policy/overview.md).

Příklady scénářů použití:

- Chcete zlepšit stav zabezpečení vaší společnosti tím, že implementujete požadavky na minimální velikosti klíčů a maximální doby platnosti certifikátů v trezorech klíčů vaší společnosti, ale nevíte, které týmy budou kompatibilní a které ne.
- V současné době nemáte řešení pro provádění auditu v rámci vaší organizace nebo provádíte ruční audity vašeho prostředí tím, že požádáte o své dodržování předpisů jednotlivé týmy v rámci vaší organizace. Hledáte způsob, jak automatizovat tuto úlohu, provádět audity v reálném čase a zaručit přesnost auditu.
- Chcete vyhovět zásadám zabezpečení vaší společnosti a zabránit jednotlivcům v vytváření certifikátů podepsaných svým držitelem, ale nemáte automatizovaný způsob, jak takové vytvoření zablokovat. 
- Chcete zmírnit některé požadavky na testovací týmy, ale chcete zachovat důkladné kontroly nad produkčním prostředím. Potřebujete jednoduchý automatizovaný způsob, jak oddělit vynucení svých prostředků.
- Chcete mít jistotu, že v případě problémů s živým pracovištěm budete moct obnovit vynucení nových zásad. Pro vypnutí vynucování zásady potřebujete řešení jedním kliknutím. 
- Spoléháte se na řešení třetích stran pro auditování vašeho prostředí a chcete použít interní nabídku Microsoftu.

## <a name="types-of-policy-effects-and-guidance"></a>Typy efektů a pokynů pro zásady

**Audit**: když je účinek zásady nastavený na audit, zásada nezpůsobí žádné změny v prostředí. Upozorní vás jenom na součásti, jako jsou certifikáty, které nejsou v souladu s definicemi zásad v rámci zadaného oboru, tak, že tyto komponenty označíte jako nedodržující předpisy na řídicím panelu pro dodržování zásad. Pokud není vybraný žádný efekt zásad, audit je výchozí.

**Odepřít**: Pokud je účinek zásady nastavený na odepřít, zásada bude blokovat vytváření nových komponent, jako jsou certifikáty, a zablokovat nové verze existujících součástí, které neodpovídají definici zásad. Stávající nekompatibilní prostředky v rámci trezoru klíčů nejsou ovlivněny. Možnosti auditu budou i nadále fungovat.

## <a name="available-built-in-policy-definitions"></a>Dostupné definice zásad "předdefinované"

Key Vault vytvořil sadu zásad, které se dají použít ke správě klíčů, certifikátu a objektů tajných kódů. Tyto zásady jsou předdefinované, což znamená, že nevyžadují, abyste napsali vlastní JSON, abyste je povolili a byli k dispozici v Azure Portal, abyste je mohli přiřadit. Můžete si pořád přizpůsobit určité parametry podle potřeb vaší organizace.

# <a name="certificate-policies"></a>[Zásady certifikátu](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>Certifikáty by měly mít zadanou maximální dobu platnosti (Preview).

Tato zásada vám umožní spravovat maximální dobu platnosti certifikátů uložených v trezoru klíčů. Pro omezení maximální doby platnosti vašich certifikátů je dobrým zvykem zabezpečení. Pokud by byl privátní klíč certifikátu napadený bez detekce, použití krátkodobých certifikátů minimalizuje časový rámec pro průběžnou škodu a snižuje hodnotu certifikátu na útočníka.

### <a name="certificates-should-use-allowed-key-types-preview"></a>Certifikáty by měly používat povolené typy klíčů (Preview)

Tato zásada vám umožní omezit typ certifikátů, které můžou být ve vašem trezoru klíčů. Pomocí této zásady můžete zajistit, že soukromé klíče certifikátu jsou RSA, ECC nebo modul HSM. Můžete si vybrat z následujícího seznamu typů certifikátů, které jsou povolené.

- RSA
- RSA – HSM
- ECC
- ECC – HSM

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>Certifikáty by měly mít zadané aktivační události doby života (Preview).

Tato zásada vám umožní spravovat akci životního cyklu určenou pro certifikáty, které jsou buď v rámci určitého počtu dní, nebo jste dosáhli určitého procenta použitelnosti svého životního cyklu.

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>Certifikáty by měly být vydány zadanou integrovanou certifikační autoritou (Preview).

Pokud používáte Key Vault integrovanou certifikační autoritu (DigiCert nebo GlobalSign) a chcete, aby uživatelé používali jednoho nebo některé z těchto zprostředkovatelů, můžete pomocí této zásady Auditovat nebo vymáhat výběr. Tato zásada se dá taky použít k auditování nebo odepření vytváření certifikátů podepsaných svým držitelem v trezoru klíčů.

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>Certifikáty by měly být vydány zadanou neintegrovanou certifikační autoritou (Preview).

Pokud používáte interní certifikační autoritu nebo certifikační autoritu, která není integrovaná s trezorem klíčů a chcete, aby uživatelé používali certifikační autoritu ze seznamu, který zadáte, můžete tuto zásadu použít k vytvoření seznamu povolených certifikačních autorit podle názvu vystavitele. Tato zásada se dá taky použít k auditování nebo odepření vytváření certifikátů podepsaných svým držitelem v trezoru klíčů.

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>Certifikáty používající kryptografii eliptické křivky by měly mít povolené názvy křivek (Preview).

Pokud používáte kryptografii s eliptickou křivkou nebo certifikáty ECC, můžete upravit povolený seznam názvů křivek ze seznamu níže. Výchozí možnost povoluje všechny následující názvy křivek.

- P-256
- P-256
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>Certifikáty používající kryptografii RSA spravovat minimální velikost klíče pro certifikáty RSA (Preview)

Pokud používáte certifikáty RSA, můžete zvolit minimální velikost klíče, kterou musí vaše certifikáty obsahovat. Můžete vybrat jednu z následujících možností v seznamu níže.

- bit 2048
- bit 3072
- bit 4096

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Spravovat certifikáty, které jsou v zadaném počtu dní vypršení platnosti (Preview)

Vaše služba se může setkat s výpadkem, pokud se nedostatečně monitorovaný certifikát neotáčí před vypršením jeho platnosti. Tyto zásady jsou důležité pro zajištění, že se budou monitorovat vaše certifikáty uložené v trezoru klíčů. Doporučuje se použít tuto zásadu několikrát s různými mezními hodnotami vypršení platnosti, například při 180, 90, 60 a na 30 dnech prahových hodnot. Tato zásada se dá použít k monitorování a třídění vypršení platnosti certifikátu ve vaší organizaci.

# <a name="key-policies"></a>[Zásady klíčů](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Klíče by neměly být aktivní po dobu delší, než je zadaný počet dní (Preview).

Pokud se chcete ujistit, že vaše klíče nebyly aktivní po dobu delší, než je zadaný počet dní, můžete pomocí této zásady Auditovat, jak dlouho byl klíč aktivní.

**Pokud má klíč nastavené datum aktivace**, tato zásada vypočítá počet dní, které uplynuly od **data aktivace** klíče k aktuálnímu datu. Pokud počet dní překročí nastavenou prahovou hodnotu, klíč bude označen jako nekompatibilní se zásadou.

**Pokud klíč nemá nastavené datum aktivace**, tato zásada vypočítá počet dní, které uplynuly od **data vytvoření** klíče k aktuálnímu datu. Pokud počet dní překročí nastavenou prahovou hodnotu, klíč bude označen jako nekompatibilní se zásadou.

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>Klíče by měly být zadaného kryptografického typu RSA nebo ES (Preview).

Tato zásada vám umožní omezit typ klíčů, které můžou být ve vašem trezoru klíčů. Pomocí této zásady můžete zajistit, aby byly klíče RSA, ECC nebo v modulu HARDWAROVÉho zabezpečení. Můžete si vybrat z následujícího seznamu typů certifikátů, které jsou povolené.

- RSA
- RSA – HSM
- ECC
- ECC – HSM

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>Klíče používající kryptografii eliptické křivky by měly mít zadané názvy křivek (Preview).

Pokud používáte kryptografii s eliptickou křivkou nebo klíče ECC, můžete upravit povolený seznam názvů křivek ze seznamu níže. Výchozí možnost povoluje všechny následující názvy křivek.

- P-256
- P-256
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>Klíče by měly mít nastavená data vypršení platnosti (Preview)

Tato zásada Audituje všechny klíče v trezorech klíčů a klíčích příznaků, u kterých není datum vypršení platnosti nastaveno jako nevyhovující. Tyto zásady můžete použít také k blokování vytváření klíčů, které nemají nastavené datum vypršení platnosti.

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Klíče by měly mít více než zadaný počet dnů před vypršením platnosti (Preview).

Pokud je klíč příliš blízko k vypršení platnosti, může dojít k výpadku organizace při jeho střídání. Klíče by se měly otočit za zadaný počet dnů před vypršením platnosti a poskytnout tak dostatek času na reakci na selhání. Tato zásada bude auditovat klíče, které se blíží datu vypršení platnosti, a umožní vám nastavit tuto prahovou hodnotu ve dnech. Tyto zásady můžete použít také k tomu, abyste zabránili vytváření nových klíčů, které jsou příliš blízko data vypršení jejich platnosti.

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>Klíče by měly být zálohovány modulem hardwarového zabezpečení (Preview)

HSM je modul hardwarového zabezpečení, který ukládá klíče. Modul hardwarového zabezpečení (HSM) poskytuje fyzickou vrstvu ochrany pro kryptografické klíče. Kryptografický klíč nemůže opustit fyzický modul HSM, který poskytuje vyšší úroveň zabezpečení než softwarový klíč. Některé organizace mají požadavky na dodržování předpisů, které jsou pověřeny používáním klíčů HSM. Pomocí této zásady můžete auditovat všechny klíče uložené ve vašem trezoru klíčů, který není modul HSM zálohovaný. Tyto zásady můžete použít také k blokování vytváření nových klíčů, které nejsou zálohovány modulem HSM. Tato zásada bude platit pro všechny typy klíčů, RSA a ECC.

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>Klíče používající kryptografii RSA by měly mít zadanou minimální velikost klíče (Preview).

Použití klíčů RSA s menšími velikostmi klíčů není bezpečný postup pro návrh. Je možné, že budete dodržovat standardy pro audit a certifikace, které využívají minimální velikost klíče. Následující zásady vám umožní nastavit minimální požadavek na velikost klíče v trezoru klíčů. Můžete auditovat klíče, které nesplňují tento minimální požadavek. Tato zásada se dá použít taky k blokování vytváření nových klíčů, které nesplňují požadavek na minimální velikost klíče.

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>Klíč by měl mít zadanou maximální dobu platnosti (Preview).

Požadavky na dodržování předpisů vaší organizace můžete spravovat tak, že zadáte maximální dobu ve dnech, po kterou může klíč platit v trezoru klíčů. Klíče, které jsou platné déle, než je nastavená prahová hodnota, budou označeny jako nevyhovující. Tyto zásady můžete použít také k blokování vytváření nových klíčů, u kterých je nastaveno datum vypršení platnosti delší, než je maximální období platnosti, které zadáte.

# <a name="secret-policies"></a>[Zásady tajného kódu](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Tajné kódy by neměly být aktivní po dobu delší, než je zadaný počet dní (Preview).

Pokud chcete zajistit, aby vaše tajná data nebyla aktivní déle než zadaný počet dní, můžete pomocí této zásady Auditovat, jak dlouho byl váš tajný klíč aktivní.

**Pokud má váš tajný kód nastavené datum aktivace**, tato zásada vypočítá počet dní, které uplynuly od **data aktivace** tajného kódu k aktuálnímu datu. Pokud počet dní překročí nastavenou prahovou hodnotu, bude tajný kód označen jako nekompatibilní se zásadou.

**Pokud váš tajný klíč nemá nastavené datum aktivace**, tato zásada vypočítá počet dní, které uplynuly od **data vytvoření** tajného kódu k aktuálnímu datu. Pokud počet dní překročí nastavenou prahovou hodnotu, bude tajný kód označen jako nekompatibilní se zásadou.

### <a name="secrets-should-have-content-type-set-preview"></a>Tajné klíče by měly mít nastaven typ obsahu (Preview)

Libovolný prostý text nebo kódovaný soubor může být uložen jako tajný kód trezoru klíčů. Vaše organizace ale může chtít nastavit různé zásady a omezení rotace v heslech, připojovacích řetězcích nebo certifikátech uložených jako klíče. Značka typu obsahu může uživateli získat informace o tom, co je uloženo v tajných objektech, aniž by bylo nutné číst hodnotu tajného klíče. Pomocí této zásady můžete auditovat tajné kódy, které nemají nastavenou značku typu obsahu. Pomocí této zásady můžete také zabránit vytváření nových tajných klíčů, pokud nemají sadu značek typu obsahu.

### <a name="secrets-should-have-expiration-date-set-preview"></a>V tajných klíčích by mělo být nastaveno datum vypršení platnosti (Preview)

Tato zásada Audituje všechny tajné klíče v trezoru klíčů a označí tajné klíče, u kterých není datum vypršení platnosti nastaveno na nevyhovující. Tyto zásady můžete použít také k blokování vytváření tajných kódů, které nemají nastavené datum vypršení platnosti.

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Tajné kódy by měly mít více než zadaný počet dnů před vypršením platnosti (Preview).

Pokud je tajný kód příliš blízko k vypršení platnosti, může dojít k výpadku organizačního zpoždění při jeho střídání. Tajné kódy by se měly v zadaném počtu dnů před vypršením lhůty otočit, aby se zajistila dostatečná doba reakce na selhání. Tato zásada provede audit tajných tajných kódů, které jsou příliš blízko data vypršení platnosti, a umožňuje nastavit tuto prahovou hodnotu ve dnech. Tyto zásady můžete použít také k tomu, abyste zabránili vytváření nových tajných klíčů, které jsou příliš blízko data vypršení jejich platnosti.

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>Tajné kódy by měly mít zadanou maximální dobu platnosti (Preview).

Požadavky na dodržování předpisů vaší organizace můžete spravovat tak, že zadáte maximální dobu ve dnech, po kterou je tajný kód platný v trezoru klíčů. Tajné kódy, které jsou platné déle než nastavená prahová hodnota, budou označeny jako nedodržující předpisy. Tyto zásady můžete použít také k blokování vytváření nových tajných klíčů, u kterých je nastaveno datum vypršení platnosti delší, než je maximální období platnosti, které zadáte.

---

## <a name="example-scenario"></a>Ukázkový scénář

Můžete spravovat Trezor klíčů používaný více týmy, které obsahují certifikáty 100, a chcete se ujistit, že žádný z certifikátů v trezoru klíčů není platný po dobu delší než 2 roky.

1. Přiřadíte **certifikát by měl mít zadanou maximální dobu platnosti** , určíte, že maximální doba platnosti certifikátu je 24 měsíců, a nastavte účinek zásady na audit. 
1. Zobrazíte [sestavu dodržování předpisů na Azure Portal](#view-compliance-results)a zjistíte, že 20 certifikátů nedodržuje předpisy a jsou platné po dobu > 2 roky a zbývající certifikáty jsou v souladu s předpisy. 
1. Kontaktujte vlastníky těchto certifikátů a sdělte mu nový požadavek na zabezpečení, že certifikáty nemůžou být platné déle než 2 roky. Některé týmy odpověděly a 15 certifikátů byly obnoveny s maximální dobou platnosti 2 roky nebo méně. Jiné týmy nereagují a ve vašem trezoru klíčů stále máte 5 certifikátů, které nedodržují předpisy.
1. Změníte účinek zásad, které jste přiřadili k "Odepřít". Pět certifikátů, které nedodržují předpisy, se neodvolává a budou dál fungovat. Nedají se ale prodloužit o dobu platnosti, která je delší než 2 roky. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Povolení a Správa zásad Key Vault pomocí Azure Portal

### <a name="select-a-policy-definition"></a>Výběr definice zásady

1. Přihlaste se k webu Azure Portal. 
1. Na panelu hledání vyhledejte "zásady" a vyberte **zásady**.

    ![Snímek obrazovky, který zobrazuje panel hledání.](../media/policy-img1.png)

1. V okně zásady vyberte **definice**.

    ![Snímek obrazovky, který zvýrazní možnost definice](../media/policy-img2.png)

1. V Filtr kategorie zrušte výběr **Vybrat vše** a vyberte **Key Vault**. 

    ![Snímek obrazovky, který zobrazuje filtr kategorie a vybranou kategorii Key Vault.](../media/policy-img3.png)

1. Nyní byste měli být schopni zobrazit všechny zásady, které jsou k dispozici pro Public Preview Azure Key Vault. Ujistěte se, že jste si přečetli a porozuměli část doprovodné materiály k zásadám, a vyberte zásadu, kterou chcete přiřadit k oboru.  

    ![Snímek obrazovky zobrazující zásady, které jsou k dispozici pro Public Preview.](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Přiřazení zásad k oboru 

1. Vyberte zásadu, kterou chcete použít. v tomto příkladu se zobrazí zásada **Spravovat období platnosti certifikátu** . Klikněte na tlačítko přiřadit v levém horním rohu.

    ![Snímek obrazovky, který zobrazuje zásady období platnosti certifikátu pro správu.](../media/policy-img5.png)
  
1. Vyberte předplatné, ve kterém chcete zásadu použít. Můžete omezit rozsah jenom na jednu skupinu prostředků v rámci předplatného. Pokud chcete zásady použít na celé předplatné a vyloučit některé skupiny prostředků, můžete taky nakonfigurovat seznam vyloučení. Nastavte selektor vynucení zásad na **povoleno** , pokud chcete, aby došlo k vlivu zásad (audit nebo Deny **) na zapnutí nebo vypnutí** efektu (audit nebo zamítnutí). 

    ![Snímek obrazovky, který ukazuje, kde se můžete rozhodnout omezit rozsah jenom na jednu skupinu prostředků v rámci předplatného.](../media/policy-img6.png)

1. V horní části obrazovky klikněte na kartu parametry a určete maximální dobu platnosti v měsících, kterou chcete. Podle pokynů v předchozích částech vyberte **audit** nebo **Odepřít** pro účinek zásad. Pak vyberte tlačítko revize + vytvořit. 

    ![Snímek obrazovky zobrazující kartu parametry, kde můžete zadat maximální dobu platnosti v měsících, kterou chcete použít.](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Zobrazit výsledky dodržování předpisů

1. Vraťte se do okna zásady a vyberte kartu dodržování předpisů. Klikněte na přiřazení zásad, pro které chcete zobrazit výsledky dodržování předpisů.

    ![Snímek obrazovky zobrazující kartu dodržování předpisů, kde můžete vybrat přiřazení zásad, pro které chcete zobrazit výsledky dodržování předpisů.](../media/policy-img8.png)

1. Na této stránce můžete filtrovat výsledky podle kompatibilních a nekompatibilních trezorů. Tady vidíte seznam trezorů klíčů, které nedodržují předpisy, v rámci přiřazení zásady. Trezor se považuje za nevyhovující, pokud některá z komponent (certifikátů) v trezoru nedodržuje předpisy. Pro zobrazení individuálních komponent, které nedodržují předpisy, můžete vybrat jednotlivý trezor. 


    ![Snímek obrazovky, který zobrazuje seznam trezorů klíčů, které nedodržují předpisy v rámci rozsahu přiřazení zásady.](../media/policy-img9.png)

1. Zobrazení názvu součástí v trezoru, které nejsou kompatibilní


    ![Snímek obrazovky, který ukazuje, kde můžete zobrazit názvy komponent v trezoru, které nedodržují předpisy.](../media/policy-img10.png)

1. Pokud potřebujete zjistit, jestli uživatelé mají odepřenou možnost vytvářet prostředky v trezoru klíčů, můžete kliknout na kartu **události komponenty (Preview)** a zobrazit souhrn zamítnutých operací certifikátu s žadatelem a časovými razítky žádostí. 


    ![Přehled toho, jak Azure Key Vault funguje](../media/policy-img11.png)

## <a name="feature-limitations"></a>Omezení funkce

Přiřazení zásady s účinkem "Odepřít" může trvat až 30 minut (průměrnou velikost) a 1 hodinu (nejhorší případ), aby bylo možné začít odmítat vytváření prostředků, které nedodržují předpisy. Vyhodnocení zásad pro existující součásti v trezoru může trvat až 1 hodinu (průměrnou velikost) a 2 hodiny (nejhorší případ), než se výsledky dodržování předpisů zobrazí v uživatelském rozhraní portálu. Pokud se výsledky dodržování předpisů zobrazují jako Nezahájeno, může to být způsobeno následujícími důvody:
- Hodnocení zásad ještě není dokončené. Počáteční latence hodnocení může v nejhorším scénáři trvat až 2 hodiny. 
- V oboru přiřazení zásady neexistují žádné trezory klíčů.
- V rámci přiřazování zásad neexistují žádné trezory klíčů s certifikáty.

> [!NOTE]
> Azure Policy [režimy poskytovatele](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)prostředků, jako jsou například pro Azure Key Vault, poskytují informace o dodržování předpisů na stránce [dodržování předpisů komponent](../../governance/policy/how-to/get-compliance-data.md#component-compliance) .

## <a name="next-steps"></a>Další kroky

- Další informace o [službě Azure Policy](../../governance/policy/overview.md)
- Viz ukázky Key Vault: [Key Vault integrovaných definic zásad](../../governance/policy/samples/built-in-policies.md#key-vault)
