---
title: Integrace služby Azure Key Vault se službou Azure Policy
description: Informace o tom, jak integrovat Azure Key Vault s Azure Policy
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 62ea64f6f0ccf3a9f4ceabc64a97e1e5570b92b4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88586062"
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

Key Vault vytvořil sadu zásad, které můžete přiřadit k běžným scénářům pro správu certifikátů. Tyto zásady jsou předdefinované, což znamená, že nevyžadují, abyste napsali vlastní JSON, abyste je povolili a byli k dispozici v Azure Portal, abyste je mohli přiřadit. Můžete si pořád přizpůsobit určité parametry podle potřeb vaší organizace. 

V této části jsou uvedené osm zásad Preview.

### <a name="manage-certificate-validity-period-preview"></a>Správa období platnosti certifikátu (Preview)

Tato zásada vám umožní spravovat maximální dobu platnosti certifikátů uložených v trezoru klíčů. Pro omezení maximální doby platnosti vašich certifikátů je dobrým zvykem zabezpečení. Pokud by byl privátní klíč certifikátu napadený bez detekce, použití krátkodobých certifikátů minimalizuje časový rámec pro průběžnou škodu a snižuje hodnotu certifikátu na útočníka. 

### <a name="manage-allowed-certificate-key-types-preview"></a>Správa povolených typů klíčů certifikátů (Preview)
Tato zásada vám umožní omezit typ certifikátů, které můžou být ve vašem trezoru klíčů. Pomocí této zásady můžete zajistit, že soukromé klíče certifikátu jsou RSA, ECC nebo modul HSM. Můžete si vybrat z následujícího seznamu typů certifikátů, které jsou povolené.
- RSA
- RSA – HSM
- ECC 
- ECC – HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>Správa aktivačních událostí akce životnosti certifikátů (Preview)

Tato zásada vám umožní spravovat akci životního cyklu určenou pro certifikáty, které jsou buď v rámci určitého počtu dní, nebo jste dosáhli určitého procenta použitelnosti svého životního cyklu. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Správa certifikátů vydaných integrovanou certifikační autoritou (Preview)

Pokud používáte Key Vault integrovanou certifikační autoritu (DigiCert nebo GlobalSign) a chcete, aby uživatelé používali jednoho nebo některé z těchto zprostředkovatelů, můžete pomocí této zásady Auditovat nebo vymáhat výběr. Tato zásada se dá taky použít k auditování nebo odepření vytváření certifikátů podepsaných svým držitelem v trezoru klíčů. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Správa certifikátů vydaných integrovanou certifikační autoritou (Preview)

Pokud používáte interní certifikační autoritu nebo certifikační autoritu, která není integrovaná s trezorem klíčů a chcete, aby uživatelé používali certifikační autoritu ze seznamu, který zadáte, můžete tuto zásadu použít k vytvoření seznamu povolených certifikačních autorit podle názvu vystavitele. Tato zásada se dá taky použít k auditování nebo odepření vytváření certifikátů podepsaných svým držitelem v trezoru klíčů. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Spravovat povolené názvy křivek pro certifikáty kryptografie s eliptickou křivkou (Preview)
Pokud používáte kryptografii s eliptickou křivkou nebo certifikáty ECC, můžete upravit povolený seznam názvů křivek ze seznamu níže. Výchozí možnost povoluje všechny následující názvy křivek. 
- P-256
- P-256
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>Správa minimální velikosti klíče pro certifikáty RSA (Preview)
Pokud používáte certifikáty RSA, můžete zvolit minimální velikost klíče, kterou musí vaše certifikáty obsahovat. Můžete vybrat jednu z následujících možností v seznamu níže. 
- bit 2048
- bit 3072
- bit 4096

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Spravovat certifikáty, které jsou v zadaném počtu dní vypršení platnosti (Preview)
Vaše služba se může setkat s výpadkem, pokud se nedostatečně monitorovaný certifikát neotáčí před vypršením jeho platnosti. Tyto zásady jsou důležité pro zajištění, že se budou monitorovat vaše certifikáty uložené v trezoru klíčů. Doporučuje se použít tuto zásadu několikrát s různými mezními hodnotami vypršení platnosti, například při 180, 90, 60 a na 30 dnech prahových hodnot. Tato zásada se dá použít k monitorování a třídění vypršení platnosti certifikátu ve vaší organizaci. 

## <a name="example-scenario"></a>Ukázkový scénář

Můžete spravovat Trezor klíčů používaný více týmy, které obsahují certifikáty 100, a chcete se ujistit, že žádný z certifikátů v trezoru klíčů není platný po dobu delší než 2 roky.

1. Přiřadíte zásadu [období platnosti certifikátu spravovat](#manage-certificate-validity-period-preview) , určíte, že maximální doba platnosti certifikátu je 24 měsíců, a nastavte účinek zásady na audit. 
1. Zobrazíte [sestavu dodržování předpisů na Azure Portal](#view-compliance-results)a zjistíte, že 20 certifikátů nedodržuje předpisy a jsou platné po dobu > 2 roky a zbývající certifikáty jsou v souladu s předpisy. 
1. Kontaktujte vlastníky těchto certifikátů a sdělte mu nový požadavek na zabezpečení, že certifikáty nemůžou být platné déle než 2 roky. Některé týmy odpověděly a 15 certifikátů byly obnoveny s maximální dobou platnosti 2 roky nebo méně. Jiné týmy nereagují a ve vašem trezoru klíčů stále máte 5 certifikátů, které nedodržují předpisy.
1. Změníte účinek zásad, které jste přiřadili k "Odepřít". Pět certifikátů, které nedodržují předpisy, se neodvolává a budou dál fungovat. Nedají se ale prodloužit o dobu platnosti, která je delší než 2 roky. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Povolení a Správa zásad Key Vault pomocí Azure Portal

### <a name="select-a-policy-definition"></a>Výběr definice zásady

1. Přihlaste se k webu Azure Portal. 
1. Na panelu hledání vyhledejte "zásady" a vyberte **zásady**.

    ![Přehled toho, jak Azure Key Vault funguje](../media/policy-img1.png)

1. V okně zásady vyberte **definice**.

    ![Přehled toho, jak Azure Key Vault funguje](../media/policy-img2.png)

1. V Filtr kategorie zrušte výběr **Vybrat vše** a vyberte **Key Vault**. 

    ![Přehled toho, jak Azure Key Vault funguje](../media/policy-img3.png)

1. Nyní byste měli být schopni zobrazit všechny zásady, které jsou k dispozici pro Public Preview Azure Key Vault. Ujistěte se, že jste si přečetli a porozuměli část doprovodné materiály k zásadám, a vyberte zásadu, kterou chcete přiřadit k oboru.  

    ![Přehled toho, jak Azure Key Vault funguje](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Přiřazení zásad k oboru 

1. Vyberte zásadu, kterou chcete použít. v tomto příkladu se zobrazí zásada **Spravovat období platnosti certifikátu** . Klikněte na tlačítko přiřadit v levém horním rohu.

    ![Přehled toho, jak Azure Key Vault funguje](../media/policy-img5.png)
  
1. Vyberte předplatné, ve kterém chcete zásadu použít. Můžete omezit rozsah jenom na jednu skupinu prostředků v rámci předplatného. Pokud chcete zásady použít na celé předplatné a vyloučit některé skupiny prostředků, můžete taky nakonfigurovat seznam vyloučení. Nastavte selektor vynucení zásad na **povoleno** , pokud chcete, aby došlo k vlivu zásad (audit nebo Deny **) na zapnutí nebo vypnutí** efektu (audit nebo zamítnutí). 

    ![Přehled toho, jak Azure Key Vault funguje](../media/policy-img6.png)

1. V horní části obrazovky klikněte na kartu parametry a určete maximální dobu platnosti v měsících, kterou chcete. Podle pokynů v předchozích částech vyberte **audit** nebo **Odepřít** pro účinek zásad. Pak vyberte tlačítko revize + vytvořit. 

    ![Přehled toho, jak Azure Key Vault funguje](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Zobrazit výsledky dodržování předpisů

1. Vraťte se do okna zásady a vyberte kartu dodržování předpisů. Klikněte na přiřazení zásad, pro které chcete zobrazit výsledky dodržování předpisů.

    ![Přehled toho, jak Azure Key Vault funguje](../media/policy-img8.png)

1. Na této stránce můžete filtrovat výsledky podle kompatibilních a nekompatibilních trezorů. Tady vidíte seznam trezorů klíčů, které nedodržují předpisy, v rámci přiřazení zásady. Trezor se považuje za nevyhovující, pokud některá z komponent (certifikátů) v trezoru nedodržuje předpisy. Pro zobrazení individuálních komponent, které nedodržují předpisy, můžete vybrat jednotlivý trezor. 


    ![Přehled toho, jak Azure Key Vault funguje](../media/policy-img9.png)

1. Zobrazení názvu součástí v trezoru, které nejsou kompatibilní


    ![Přehled toho, jak Azure Key Vault funguje](../media/policy-img10.png)

1. Pokud potřebujete zjistit, jestli uživatelé mají odepřenou možnost vytvářet prostředky v trezoru klíčů, můžete kliknout na kartu **události komponenty (Preview)** a zobrazit souhrn zamítnutých operací certifikátu s žadatelem a časovými razítky žádostí. 


    ![Přehled toho, jak Azure Key Vault funguje](../media/policy-img11.png)

## <a name="feature-limitations"></a>Omezení funkcí

Přiřazení zásady s účinkem "Odepřít" může trvat až 30 minut (průměrnou velikost) a 1 hodinu (nejhorší případ), aby bylo možné začít odmítat vytváření prostředků, které nedodržují předpisy. Vyhodnocení zásad pro existující součásti v trezoru může trvat až 1 hodinu (průměrnou velikost) a 2 hodiny (nejhorší případ), než se výsledky dodržování předpisů zobrazí v uživatelském rozhraní portálu. Pokud se výsledky dodržování předpisů zobrazují jako Nezahájeno, může to být způsobeno následujícími důvody:
- Hodnocení zásad ještě není dokončené. Počáteční latence hodnocení může v nejhorším scénáři trvat až 2 hodiny. 
- V oboru přiřazení zásady neexistují žádné trezory klíčů.
- V rámci přiřazování zásad neexistují žádné trezory klíčů s certifikáty. 

## <a name="next-steps"></a>Další kroky

- Další informace o [službě Azure Policy](../../governance/policy/overview.md)
- Viz ukázky Key Vault: [Key Vault integrovaných definic zásad](../../governance/policy/samples/built-in-policies.md#key-vault)