---
title: Integrace azure trezoru klíčů s azure zásady
description: Zjistěte, jak integrovat Azure Key Vault s Azure Policy
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.openlocfilehash: 6b54dc27f8a3e88dedb0552b1ac7fb675d75121a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424591"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrace azure trezoru klíčů s azure zásady

[Azure Policy](../../governance/policy/index.yml) je nástroj zásad správného řízení, který uživatelům umožňuje auditovat a spravovat své prostředí Azure ve velkém měřítku. Azure Policy poskytuje možnost umístit svodidla na prostředky Azure, aby bylo zajištěno, že jsou v souladu s přiřazenými pravidly zásad. Umožňuje uživatelům provádět audit, vynucení v reálném čase a nápravu jejich prostředí Azure. Výsledky auditů prováděných zásadami budou uživatelům k dispozici na řídicím panelu s dodržováním předpisů, kde budou moci zobrazit podrobné informace o tom, které prostředky a součásti jsou kompatibilní a které nikoli.  Další informace najdete v [přehledu služby Zásadazure](../../governance/policy/overview.md).

Příklad scénáře použití:

- Chcete zlepšit stav zabezpečení vaší společnosti implementací požadavků týkajících se minimálnívelikosti klíčů a maximální doby platnosti certifikátů v trezorech klíčů vaší společnosti, ale nevíte, které týmy budou kompatibilní a které ne. 
- V současné době nemáte řešení pro provádění auditu v celé organizaci nebo provádíte ruční audity vašeho prostředí tím, že žádáte jednotlivé týmy ve vaší organizaci, aby nahlásily své dodržování předpisů. Hledáte způsob, jak automatizovat tento úkol, provádět audity v reálném čase a zaručit přesnost auditu.
- Chcete vynutit zásady zabezpečení společnosti a zabránit jednotlivcům ve vytváření certifikátů podepsaných svým držitelem, ale nemáte automatizovaný způsob, jak blokovat jejich vytváření. 
- Chcete uvolnit některé požadavky pro testovací týmy, ale chcete zachovat přísnou kontrolu nad produkčním prostředím. Potřebujete jednoduchý automatizovaný způsob, jak oddělit vynucení vašich prostředků. 
- Chcete mít jistotu, že můžete vrátit zpět vynucení nových zásad v případě problému živého webu. K vypnutí vynucení zásad potřebujete řešení jedním kliknutím. 
- Spoléháte se na řešení třetí strany pro auditování vašeho prostředí a chcete použít interní nabídku společnosti Microsoft. 

## <a name="types-of-policy-effects-and-guidance"></a>Typy účinků politiky a pokynů

**Audit**: Pokud je účinek zásady nastaven na audit, zásada nezpůsobí žádné narušující změny ve vašem prostředí. Bude vás pouze upozornit na součásti, jako jsou certifikáty, které nejsou v souladu s definicemi zásad v rámci zadaného oboru, označením těchto součástí jako nekompatibilní v řídicím panelu dodržování zásad. Audit je výchozí, pokud není vybrán žádný efekt zásad. 

**Odepřít**: Pokud je účinek zásady nastaven na odepření, zásada zablokuje vytváření nových součástí, jako jsou certifikáty, a také blokovat nové verze existujících součástí, které nejsou v souladu s definicí zásady. Existující nekompatibilní prostředky v trezoru klíčů nejsou ovlivněny. Možnosti "auditu" budou nadále fungovat.

## <a name="available-built-in-policy-definitions"></a>Dostupné definice zásad "Předdefinované"

Trezor klíčů vytvořil sadu zásad, které můžete přiřadit pro běžné scénáře pro správu certifikátů. Tyto zásady jsou "vestavěné", což znamená, že nevyžadují, abyste napsali vlastní JSON, abyste je povolili, a jsou k dispozici na portálu Azure, který můžete přiřadit. Stále můžete přizpůsobit určité parametry tak, aby vyhovovaly potřebám vaší organizace. 

Osm zásad náhledu jsou následující.

### <a name="manage-certificate-validity-period-preview"></a>Správa doby platnosti certifikátu (náhled)

Tato zásada umožňuje spravovat maximální dobu platnosti certifikátů uložených v trezoru klíčů. Je vhodné omezit maximální dobu platnosti certifikátů. Pokud by došlo k ohrožení zabezpečení soukromého klíče vašeho certifikátu bez detekce, použití krátkodobých certifikátů minimalizuje časový rámec pro trvalé poškození a snižuje hodnotu certifikátu pro útočníka. 

### <a name="manage-allowed-certificate-key-types-preview"></a>Správa povolených typů klíčů certifikátů (náhled)
Tato zásada umožňuje omezit typ certifikátů, které mohou být v trezoru klíčů. Pomocí této zásady můžete zajistit, aby vaše soukromé klíče certifikátu byly zálohovány rsa, ECC nebo jsou zálohovány hsm. Můžete si vybrat z následujícího seznamu, které typy certifikátů jsou povoleny.
- RSA
- RSA - HSM
- Ecc 
- ECC - HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>Správa aktivačních událostí doživotní certifikátu (náhled)

Tato zásada umožňuje spravovat celoživotní akci určenou pro certifikáty, které jsou buď v rámci určitého počtu dní od vypršení jejich platnosti, nebo dosáhly určitého procenta jejich použitelné životnosti. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Správa certifikátů vydaných integrovaným certifikačním systémem (preview)

Pokud používáte integrovanou certifikační autoritu trezoru klíčů (Digicert nebo GlobalSign) a chcete, aby uživatelé používali jednoho nebo některého z těchto poskytovatelů, můžete pomocí této zásady auditovat nebo vynutit váš výběr. Tuto zásadu lze také použít k auditu nebo odepření vytváření certifikátů podepsaných svým držitelem v trezoru klíčů. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Správa certifikátů vydaných integrovaným certifikačním systémem (preview)

Pokud používáte interní certifikační autoritu nebo certifikační autoritu, která není integrována s trezorem klíčů, a chcete, aby uživatelé používali certifikační autoritu z poskytnutého seznamu, můžete pomocí této zásady vytvořit povolený seznam certifikačních autorit podle názvu vystavittele. Tuto zásadu lze také použít k auditu nebo odepření vytváření certifikátů podepsaných svým držitelem v trezoru klíčů. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Správa povolených názvů křivek pro kryptografické certifikáty eliptické křivky (náhled)
Pokud používáte kryptografii eliptické křivky nebo certifikáty ECC, můžete přizpůsobit povolený seznam názvů křivek z níže uvedeného seznamu. Výchozí možnost umožňuje všechny následující názvy křivek. 
- P-256
- P-256K
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>Správa minimální velikosti klíče pro certifikáty RSA (náhled)
Pokud používáte certifikáty RSA, můžete zvolit minimální velikost klíče, kterou musí mít certifikáty. Můžete vybrat jednu možnost z níže uvedeného seznamu. 
- 2048 bit
- 3072 bitů
- 4096 bitů

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Správa certifikátů, které jsou v rámci zadaného počtu dní vypršení platnosti (náhled)
Pokud certifikát, který není dostatečně monitorován, není před vypršením platnosti otočen, může dojít k výpadku služby. Tato zásada je důležitá pro zajištění toho, aby byly monitorovány certifikáty uložené v trezoru klíčů. Doporučujeme použít tuto zásadu vícekrát s různými prahovými hodnotami vypršení platnosti, například při prahových hodnotách 180, 90, 60 a 30 dnů. Tuto zásadu lze použít ke sledování a vypršení platnosti certifikátu ve vaší organizaci. 

## <a name="example-scenario"></a>Ukázkový scénář

Spravujete trezor klíčů používaný více týmy, který obsahuje 100 certifikátů, a chcete se ujistit, že žádný z certifikátů v trezoru klíčů není platný déle než 2 roky.

1. Přiřadíte zásadu [Správa doby platnosti certifikátu,](#manage-certificate-validity-period-preview) určíte, že maximální doba platnosti certifikátu je 24 měsíců, a nastavíte účinek zásady na "audit". 
1. Zobrazíte [sestavu dodržování předpisů na portálu Azure a](#view-compliance-results)zjistíte, že 20 certifikátů je nekompatibilních a platných po dobu > 2 roky a zbývající certifikáty jsou kompatibilní. 
1. Můžete kontaktovat vlastníky těchto certifikátů a sdělit nový požadavek zabezpečení, že certifikáty nemohou být platné po dobu delší než 2 roky. Některé týmy reagují a 15 certifikátů bylo obnoveno s maximální dobou platnosti 2 roky nebo méně. Ostatní týmy nereagují a stále máte 5 nekompatibilnícertifikáty v trezoru klíčů.
1. Můžete změnit účinek zásad, které jste přiřadili k "odepřít". 5 nekompatibilní certifikáty nejsou odvolány a nadále fungovat. Nemohou však být obnoveny s dobou platnosti delší než 2 roky. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Povolení a správa zásad trezoru klíčů prostřednictvím portálu Azure

### <a name="select-a-policy-definition"></a>Výběr definice zásad

1. Přihlaste se k webu Azure Portal. 
1. Hledání "Zásady" na panelu hledání a vyberte **zásady**.

    ![Přehled fungování azure key vaultu](../media/policy-img1.png)

1. V okně Zásady vyberte **Definice**.

    ![Přehled fungování azure key vaultu](../media/policy-img2.png)

1. Ve filtru kategorie zrušte výběr **vyberte vše** a vyberte **Trezor klíčů**. 

    ![Přehled fungování azure key vaultu](../media/policy-img3.png)

1. Teď byste měli vidět všechny zásady, které jsou k dispozici pro Public Preview, pro Azure Key Vault. Ujistěte se, že jste si přečetli a porozuměli výše uvedené části pokynů k zásadám a vyberte zásadu, kterou chcete přiřadit k oboru.  

    ![Přehled fungování azure key vaultu](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Přiřazení zásady k oboru 

1. Vyberte zásadu, kterou chcete použít, v tomto příkladu se zobrazí **zásada Spravovat dobu platnosti certifikátu.** Klikněte na tlačítko přiřadit v levém horním rohu.

    ![Přehled fungování azure key vaultu](../media/policy-img5.png)
  
1. Vyberte předplatné, kde chcete zásadu použít. Můžete omezit obor pouze na jednu skupinu prostředků v rámci předplatného. Pokud chcete použít zásady pro celé předplatné a vyloučit některé skupiny prostředků, můžete také nakonfigurovat seznam vyloučení. Pokud chcete, aby došlo k efektu zásady (audit nebo odepřít) nebo **zakázán efekt** (audit nebo odepřít) nastavte volič vynucení zásad na **povoleno.** 

    ![Přehled fungování azure key vaultu](../media/policy-img6.png)

1. Klikněte na kartu parametry v horní části obrazovky, abyste určili maximální dobu platnosti v požadovaných měsících. Vyberte **audit** nebo **odepřít** pro účinek zásady podle pokynů v oddílech výše. Pak vyberte tlačítko recenze + vytvořit. 

    ![Přehled fungování azure key vaultu](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Zobrazit výsledky dodržování předpisů

1. Vraťte se do okna Zásady a vyberte kartu dodržování předpisů.

    ![Přehled fungování azure key vaultu](../media/policy-img8.png)

1. Na této stránce můžete filtrovat výsledky podle kompatibilních nebo nekompatibilních trezorů. Zde můžete zobrazit seznam nekompatibilních trezorů klíčů v rámci přiřazení zásad. Úschovna je považována za nevyhovující, pokud některá z součástí (certifikátů) v úschovně není kompatibilní. Můžete vybrat jednotlivé úschovny a zobrazit jednotlivé nekompatibilní součásti (certifikáty). 


    ![Přehled fungování azure key vaultu](../media/policy-img9.png)

1. Zobrazení názvu součástí v úschovně, které nejsou kompatibilní


    ![Přehled fungování azure key vaultu](../media/policy-img10.png)

1. Pokud potřebujete zkontrolovat, zda je uživatelům odepřena možnost vytvářet prostředky v rámci trezoru klíčů, můžete kliknutím na kartu **Události součásti (náhled)** zobrazit souhrn zamítnutých operací certifikátu s žadatelem a časová razítka požadavků. 


    ![Přehled fungování azure key vaultu](../media/policy-img11.png)

## <a name="feature-limitations"></a>Omezení funkcí

Přiřazení zásady s efektem "odepřít" může trvat až 30 minut (průměrný případ) a 1 hodinu (nejhorší případ), než začnete odpírat vytváření nevyhovujících prostředků. Vyhodnocení zásad existujících součástí v úložišti může trvat až 1 hodinu (průměrný případ) a 2 hodiny (v nejhorším případě), než budou výsledky dodržování předpisů viditelné v unovém uživateli portálu. Pokud se výsledky dodržování předpisů zobrazí jako "Nezahájeno", může to být způsobeno následujícími důvody:
- Ocenění politiky ještě nebylo dokončeno. Počáteční čekací doba hodnocení může trvat až 2 hodiny v nejhorším případě. 
- V rozsahu přiřazení zásad nejsou žádné trezory klíčů.
- V rámci přiřazení zásad nejsou žádné trezory klíčů s certifikáty. 

## <a name="next-steps"></a>Další kroky

- Další informace o [službě Zásad azure](../../governance/policy/overview.md)
- Viz Ukázky trezoru [klíčů: Definice předdefinovaných zásad trezoru klíčů](../../governance/policy/samples/built-in-policies.md#key-vault)