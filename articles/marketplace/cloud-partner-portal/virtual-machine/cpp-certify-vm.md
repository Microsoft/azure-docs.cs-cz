---
title: Certifikace image virtuálního počítače pro Azure Marketplace
description: Vysvětluje, jak otestovat a odeslat image virtuálního počítače pro certifikaci Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: dsindona
ms.openlocfilehash: 0225069179e0d7d94a983c5161976a5c1933fac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278191"
---
# <a name="certify-your-vm-image"></a>Certifikace image virtuálního počítače

Po vytvoření a nasazení virtuálního počítače (VM) je nutné otestovat a odeslat image virtuálního počítače pro certifikaci Azure Marketplace. Tento článek vysvětluje, kde získat *certifikační testovací nástroj pro Azure Certified*, jak tento nástroj použít k certifikaci image virtuálního počítače a jak nahrát výsledky ověření do kontejneru Azure, kde se nacházejí vaše virtuální počítače. 


## <a name="download-and-run-the-certification-test-tool"></a>Stažení a spuštění certifikačního testovacího nástroje

Certifikační testovací nástroj pro Azure Certified běží na místním počítači s Windows, ale testuje virtuální počítač s Windows nebo Linux založený na Azure.  Ověří, že image virtuálního počítače uživatele je kompatibilní s Microsoft Azure – že byly splněny pokyny a požadavky týkající se přípravy virtuálního pevného disku. Výstupem nástroje je zpráva o kompatibilitě, kterou nahrajete na [portál partnerů cloudu](https://cloudpartner.azure.com) a požádáte o certifikaci virtuálních zařízení.

1. Stáhněte a nainstalujte nejnovější [certifikační testovací nástroj pro Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Otevřete certifikační nástroj a klepněte na tlačítko **Spustit nový test**.
3. Na obrazovce **Informace o testu** zadejte název **testu** pro testovací běh.
4. Vyberte **platformu** pro váš `Windows Server` `Linux`virtuální počítač, nebo . Volba platformy ovlivní zbývající možnosti.
5. Pokud váš virtuální počítač používá tuto databázovou službu, zaškrtněte políčko **Testovat pro Azure SQL Database.**

   ![Úvodní stránka nástroje pro testování certifikátu](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Připojení certifikačního nástroje k bitové kopii virtuálního zařízení

  Nástroj se připojuje k virtuálním počítačům se systémem Windows pomocí [prostředí PowerShell](https://docs.microsoft.com/powershell/) a připojuje se k virtuálním počítačům s Linuxem prostřednictvím [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Připojení certifikačního nástroje k bitové kopii virtuálního počítače s Linuxem

1. Vyberte režim **ověřování SSH:** `Password Authentication` nebo `key File Authentication`.
2. Pokud používáte ověřování pomocí hesla, zadejte hodnoty pro **název DNS virtuálního počítače**, uživatelské **jméno**a **heslo**.  Volitelně můžete změnit výchozí číslo **portu SSH.**

     ![Ověřování hesla image virtuálního počítače s Linuxem](./media/publishvm_026.png)

3. Pokud používáte ověřování pomocí souboru klíčů, zadejte hodnoty pro **název DNS virtuálního uživatele**, uživatelské **jméno**a umístění **soukromého klíče.**  Volitelně můžete zadat **přístupové heslo** nebo změnit výchozí číslo **portu SSH.**

     ![Ověřování souborů image virtuálního počítače s Linuxem](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Připojení certifikačního nástroje k bitové kopii virtuálního zařízení založeného na Windows**
1. Zadejte plně kvalifikovaný **název DNS virtuálního zařízení** `MyVMName.Cloudapp.net`(například).
2. Zadejte hodnoty **uživatelského jména** a **hesla**.

   ![Ověřování hesel pro bitovou kopii virtuálního počítače s Windows](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Spuštění certifikačního testu

Po zadejme hodnoty parametrů pro image virtuálního počítače v certifikačním nástroji, vyberte **Test připojení** k zajištění platné připojení k virtuálnímu počítači. Po ověření připojení vyberte **další** a spusťte test.  Po dokončení testu se zobrazí tabulka s výsledky testu (Pass/Fail/Warning).  Následující příklad ukazuje výsledky testů pro test virtuálního počítače s Linuxem. 

![Výsledky certifikačních testů pro bitovou kopii virtuálního počítače s Linuxem](./media/publishvm_029.png)

Pokud některý z testů selže, vaše image *není* certifikována. V takovém případě zkontrolujte požadavky a zprávy selhání, proveďte uvedené změny a znovu spusťte test. 

Po automatizovaném testu budete muset na obrazovce Dotazník poskytnout další informace o obrázku virtuálního **počítače.**  Obsahuje dvě karty, které je nutné vyplnit.  Karta **Obecné hodnocení** obsahuje otázky **true/false,** zatímco **přizpůsobení jádra** obsahuje více otázek výběru a volného tvaru.  Vyplňte otázky na obou kartách a vyberte **další**.

![Dotazník certifikačního nástroje](./media/publishvm_030.png)

Poslední obrazovka umožňuje poskytnout další informace, jako jsou například informace o přístupu SSH pro bitovou kopii virtuálního počítače s Linuxem a vysvětlení pro všechna selhání hodnocení, pokud hledáte výjimky. 

Nakonec klepněte na **tlačítko Generovat sestavu** a stáhněte si kromě odpovědí na dotazník také výsledky testů a soubory protokolu pro provedené testovací případy. Uložte výsledky ve stejném kontejneru jako virtuální pevné disky.

![Uložení výsledků certifikačních testů](./media/publishvm_031.png)


## <a name="next-steps"></a>Další kroky

Dále [vygenerujete jednotné identifikátory prostředků (URI) pro každý virtuální pevný disk,](./cpp-get-sas-uri.md) který odešlete na trh. 
