---
title: Certifikovat image virtuálního počítače pro Azure Marketplace
description: Vysvětluje, jak otestovat a odeslat image virtuálního počítače pro Azure Marketplace certifikace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: dsindona
ms.openlocfilehash: 81dec159a8a84adce97750666baba455ae3fdd93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147083"
---
# <a name="certify-your-vm-image"></a>Certifikace image virtuálního počítače

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek virtuálních počítačů Azure do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Pokud chcete spravovat migrované nabídky, postupujte podle pokynů v tématu [certifikace imagí virtuálních počítačů Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) .

Až vytvoříte a nasadíte virtuální počítač, musíte otestovat a odeslat image virtuálního počítače pro Azure Marketplace certifikace. Tento článek vysvětluje, kde získat *Nástroj certifikace pro certifikaci pro Azure Certified*, jak tento nástroj použít k certifikaci vaší image virtuálního počítače a jak nahrát výsledky ověření do kontejneru Azure, kde jsou umístěné vaše virtuální pevné disky. 


## <a name="download-and-run-the-certification-test-tool"></a>Stažení a spuštění nástroje certifikace test

Nástroj certifikace pro certifikaci pro Azure Certified běží na místním počítači s Windows, ale testuje virtuální počítač se systémem Windows nebo Linux na platformě Azure.  Ověřuje, jestli je vaše uživatelská image virtuálního počítače kompatibilní s Microsoft Azure – splňuje pokyny a požadavky týkající se přípravy vašeho virtuálního pevného disku. Výstupem nástroje je sestava kompatibility, kterou nahrajete do [portál partnerů cloudu](https://cloudpartner.azure.com) pro vyžádání certifikace virtuálního počítače.

1. Stáhněte a nainstalujte si nejnovější [Nástroj pro testování certifikace pro certifikaci Azure](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Otevřete nástroj certifikace a pak klikněte na **Spustit nový test**.
3. Na obrazovce **informace o testu** zadejte **název testu** testovacího běhu.
4. Vyberte **platformu** pro váš virtuální počítač, a `Windows Server` to `Linux`buď nebo. Volba vaší platformy má vliv na zbývající možnosti.
5. Pokud váš virtuální počítač používá tuto databázovou službu, zaškrtněte políčko **test pro Azure SQL Database** .

   ![Úvodní stránka nástroje CERT test Tool](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Připojení certifikačního nástroje k imagi virtuálního počítače

  Nástroj se připojuje k virtuálním počítačům s Windows pomocí [PowerShellu](https://docs.microsoft.com/powershell/) a připojuje se k virtuálním počítačům Linux prostřednictvím [SSH.NET](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Připojení certifikačního nástroje k imagi virtuálního počítače se systémem Linux

1. Vyberte režim **ověřování SSH** : `Password Authentication` nebo `key File Authentication`.
2. Pokud používáte ověřování na základě hesla, zadejte hodnoty pro **název DNS virtuálního počítače**, **uživatelské jméno**a **heslo**.  Volitelně můžete změnit výchozí číslo **portu SSH** .

     ![Ověření hesla pro bitovou kopii virtuálního počítače se systémem Linux](./media/publishvm_026.png)

3. Pokud používáte ověřování na základě klíčového souboru, zadejte hodnoty pro **název DNS virtuálního počítače**, **uživatelské jméno**a umístění **privátního klíče** .  Volitelně můžete zadat **heslo** nebo změnit výchozí číslo **portu SSH** .

     ![Ověření souboru image virtuálního počítače se systémem Linux](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Připojení certifikačního nástroje k imagi virtuálního počítače se systémem Windows**
1. Zadejte plně kvalifikovaný **název DNS virtuálního počítače** (například `MyVMName.Cloudapp.net`).
2. Zadejte hodnoty **uživatelského jména** a **hesla**.

   ![Ověřování hesla bitové kopie virtuálního počítače s Windows](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Spustit test certifikace

Po zadání hodnot parametrů pro image virtuálního počítače v nástroji certifikace vyberte **Test připojení** , aby se zajistilo platné připojení k vašemu virtuálnímu počítači. Po ověření připojení vyberte **Další** a spusťte test.  Po dokončení testu se zobrazí tabulka s výsledky testu (úspěch/chyba/upozornění).  Následující příklad ukazuje výsledky testu pro test virtuálního počítače se systémem Linux. 

![Výsledky testů certifikace pro bitovou kopii virtuálního počítače se systémem Linux](./media/publishvm_029.png)

Pokud některý z testů selže, váš obrázek *není certifikovaný.* V takovém případě zkontrolujte zprávy o požadavcích a chybách, proveďte uvedené změny a spusťte test znovu. 

Po automatizovaném testu budete muset na obrazovce **dotazníku** zadat další informace o imagi virtuálního počítače.  Obsahuje dvě karty, které je třeba dokončit.  Karta **obecné posouzení** obsahuje otázky na **true/false** , zatímco **přizpůsobení jádra** obsahuje více otázek výběru a volného tvaru.  Dokončete otázky na obou kartách a pak vyberte **Další**.

![Dotazník nástroje certifikace](./media/publishvm_030.png)

Poslední obrazovka vám umožní zadat další informace, například informace o přístupu SSH pro image virtuálního počítače se systémem Linux, a vysvětlení všech neúspěšných posouzení, pokud hledáte výjimky. 

Nakonec klikněte na tlačítko **Generovat sestavu** a Stáhněte si výsledky testů a soubory protokolu pro provedené testovací případy, a to spolu s odpověďmi na dotazník. Výsledky uložte do stejného kontejneru jako virtuální pevný disk (VHD).

![Uložení výsledků testů certifikace](./media/publishvm_031.png)


## <a name="next-steps"></a>Další kroky

V dalším kroku [vygenerujete identifikátor URI (Uniform Resource Identifier) pro každý virtuální pevný disk](./cpp-get-sas-uri.md) , který odešlete do webu Marketplace. 
