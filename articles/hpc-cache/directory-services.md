---
title: Použití rozšířených skupin v mezipaměti HPC Azure
description: Jak nakonfigurovat adresářové služby pro klientský přístup k cílům úložiště v mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: fd5dce0760953bf19c72e1a1062a9c03ffe861e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563364"
---
# <a name="configure-directory-services"></a>Konfigurace adresářových služeb

Nastavení **adresářových služeb** umožňuje, aby mezipaměť Azure HPC používala externí zdroj k ověřování uživatelů pro přístup k back-endovému úložišti.

Pokud váš pracovní postup zahrnuje cíle úložiště NFS a klienty, kteří jsou členy více než 16 skupin, bude možná potřeba povolit **Rozšířené skupiny** .

Po kliknutí na tlačítko pro povolení rozšířených skupin musíte zvolit zdroj, který bude mezipaměť prostředí Azure HPC používat k získání přihlašovacích údajů uživatelů a skupin.

* [Active Directory](#configure-active-directory) – zobrazí přihlašovací údaje z externího serveru služby Active Directory. Pro tuto úlohu nemůžete použít Azure Active Directory.
* [Plochý soubor](#configure-file-download) – `/etc/group` stažení `/etc/passwd` souborů a soubory z umístění v síti.
* [LDAP](#configure-ldap) – získání přihlašovacích údajů ze zdroje kompatibilního s protokolem LDAP (Lightweight Directory Access Protocol).

> [!NOTE]
> Ujistěte se, že vaše mezipaměť má přístup ke zdroji informací o skupině ze své zabezpečené podsítě.<!-- + details/examples -->

Pole pro **stažení uživatelského jména** zobrazuje stav nejaktuálnějšího stažení informací o skupině.

![snímek obrazovky s nastavením stránky adresářových služeb na portálu s možností Ano vybranými pro rozšířené skupiny a rozevírací nabídka označený zdroj stahování je otevřený](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Konfigurace Active Directory

V této části se dozvíte, jak nastavit mezipaměť pro získání přihlašovacích údajů uživatelů a skupin z externího serveru služby Active Directory (AD).

V části **Podrobnosti služby Active Directory** zadejte tyto hodnoty:

* **Primární DNS** – zadejte IP adresu serveru názvů domény, který může mezipaměť použít k překladu názvu domény služby AD.

* **Sekundární DNS** (volitelné) – zadejte adresu názvového serveru, který se má použít, pokud je primární server nedostupný.

* **Název domény DNS služby Active Directory** – zadejte plně kvalifikovaný název domény serveru služby Active Directory, ke které se mezipaměť připojí, aby získala přihlašovací údaje.

* **Název serveru mezipaměti (účet počítače)** – nastavte název, který se přiřadí k této mezipaměti HPC, když se připojí k doméně služby AD. Zadejte název, který lze snadno rozpoznat jako tuto mezipaměť. Název může mít délku až 15 znaků a může obsahovat velká a malá písmena, číslice a spojovníky (-).

V části **přihlašovací údaje** zadejte uživatelské jméno a heslo správce služby AD, které může mezipaměť Azure HPC použít pro přístup k serveru AD. Tyto informace jsou při uložení zašifrované a nelze na ně dotazovat.

Nastavení uložte kliknutím na tlačítko v horní části stránky.

![snímek obrazovky s vyplněnými hodnotami adresáře služby Active Directory v části Podrobnosti o stažení](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>Konfigurace stažení souboru

Tyto hodnoty jsou povinné, pokud chcete stahovat soubory s informacemi o uživatelích a skupinách. Soubory musí být ve formátu standard Linux/UNIX `/etc/group` a `/etc/passwrd` .

* **Identifikátor URI souboru uživatele** – zadejte úplný identifikátor URI pro tento `/etc/passwrd` soubor.
* **Identifikátor URI skupinového souboru** – zadejte úplný identifikátor URI pro tento `/etc/group` soubor.

![snímek obrazovky oddílu s podrobnostmi o stažení pro stažení plochého souboru](media/group-download-details-file.png)

## <a name="configure-ldap"></a>Konfigurace LDAP

Tyto hodnoty vyplňte, pokud chcete k získání přihlašovacích údajů uživatelů a skupin použít jiný zdroj než AD LDAP. Pokud potřebujete s těmito hodnotami pomáhat, obraťte se na správce protokolu LDAP.

* **Server LDAP** – zadejte plně kvalifikovaný název domény nebo IP adresu serveru LDAP, který chcete použít. <!-- only one, not up to 3 -->

* **Základní** rozlišující název LDAP – zadejte základní rozlišující název pro doménu LDAP ve formátu DN. Pokud neznáte Základní rozlišující název, požádejte správce protokolu LDAP.

Jediným požadovaným nastavením pro práci s protokolem LDAP je server a základní rozlišující název, ale další možnosti usnadňují zabezpečení připojení.

![snímek obrazovky oblasti Konfigurace LDAP stránky nastavení stránky adresářové služby](media/group-download-details-ldap.png)

V části **zabezpečený přístup** můžete povolit šifrování a ověření certifikátu pro připojení LDAP. Po kliknutí na **Ano** můžete zapnout šifrování, máte tyto možnosti:

* **Ověřit certifikát** – Pokud je tato operace nastavená, ověří se certifikát serveru LDAP proti certifikační autoritě v níže uvedeném poli identifikátoru URI.

* **Identifikátor URI certifikátu certifikační autority** – zadejte cestu k autoritativnímu certifikátu. Může se jednat o odkaz na certifikát ověřený certifikační autoritou nebo na certifikát podepsaný svým držitelem. Toto pole je povinné pro použití nastavení externě ověřených certifikátů.

* **Automaticky stahovat certifikát** – Pokud chcete zkusit stáhnout certifikát hned po odeslání těchto nastavení, klikněte na **Ano** .

Pokud chcete použít statické přihlašovací údaje pro zabezpečení LDAP, vyplňte část **přihlašovací údaje** . Tyto informace jsou při uložení zašifrované a nelze na ně dotazovat.

* **Rozlišující název vazby** – zadejte rozlišující název vazby, který se použije k ověření na serveru LDAP. (Použijte formát DN.)
* **Heslo vazby** – zadejte heslo pro rozlišující název vazby.

## <a name="next-steps"></a>Další kroky

* Další informace o přístupu klienta v [části připojení mezipaměti HPC Azure](hpc-cache-mount.md)
* Pokud se vaše přihlašovací údaje nestahují správně, obraťte se na správce se zdrojem přihlašovacích údajů. V případě potřeby otevřete [lístek podpory](hpc-cache-support-ticket.md) .
