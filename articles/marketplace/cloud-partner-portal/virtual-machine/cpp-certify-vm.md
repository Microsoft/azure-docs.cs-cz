---
title: Certifikace vaší image virtuálního počítače pro Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje, jak otestovat a odešlete image virtuálního počítače k certifikaci Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 69ebe7fde454d2f0e98371406de56f48c9ea97ae
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639626"
---
# <a name="certify-your-vm-image"></a>Certifikace vaší image virtuálního počítače

Po vytvoření a nasazení vašich virtuálních počítačů (VM), musíte test a odešlete image virtuálního počítače k certifikaci Azure Marketplace. Tento článek vysvětluje, kde lze získat *certifikační nástroj pro testování Azure Certified*, jak tento nástroj použijte k certifikaci vaší image virtuálního počítače a jak nahrát výsledků ověření do kontejneru Azure, kde jsou umístěné virtuální pevné disky. 


## <a name="download-and-run-the-certification-test-tool"></a>Stáhnout a spustit nástroj Certifikační testování

Nástroj Test certifikace pro Azure Certified běží na místním počítači Windows, ale testy s Windows založené na Azure nebo virtuální počítač s Linuxem.  Ověří, že uživatelské image virtuálního počítače je kompatibilní s Microsoft Azure –, že byly splněny pokyny a požadavky týkající se přípravu vašeho virtuálního pevného disku. Výstupem nástroje je sestava kompatibility, která odešlete do [portál partnerů cloudu](https://cloudpartner.azure.com) na žádost o certifikaci virtuálního počítače.

1. Stáhněte a nainstalujte nejnovější [certifikační nástroj pro testování Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Otevřete certifikační nástroj a potom klikněte na tlačítko **spustit nový Test**.
3. Z **informace o testu** obrazovky, zadejte **Test název** pro testovací běh.
4. Vyberte **platformy** pro váš virtuální počítač buď `Windows Server` nebo `Linux`. Zbývající možnosti má vliv na vaši volbu platformy.
5. Pokud se váš virtuální počítač používá tato databázová služba, vyberte **testu pro službu Azure SQL Database** zaškrtávací políčko.

   ![Počáteční stránka certifikátu testovací nástroje](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Připojení certifikačního nástroje k imagi virtuálního počítače

  Nástroj se připojuje k virtuálních počítačů s Windows s [PowerShell](https://docs.microsoft.com/powershell/) a připojí virtuální počítače s Linuxem prostřednictvím [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Připojení certifikačního nástroje k imagi virtuálního počítače s Linuxem

1. Vyberte **ověření procesu SSH** režimu: `Password Authentication` nebo `key File Authentication`.
2. Pokud používáte ověřování pomocí hesla, zadejte hodnoty pro **název DNS virtuálního počítače**, **uživatelské jméno**, a **heslo**.  Volitelně můžete změnit výchozí **portu SSH** číslo.

     ![Ověřování hesla z Image virtuálního počítače s Linuxem](./media/publishvm_026.png)

3. Pokud používáte ověřování pomocí klíče založené na souboru, zadejte hodnoty pro **název DNS virtuálního počítače**, **uživatelské jméno**, a **privátní klíč** umístění.  Volitelně můžete zadat **heslo** nebo změnit výchozí **portu SSH** číslo.

     ![Ověřování pomocí souboru z Image virtuálního počítače s Linuxem](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Připojení certifikačního nástroje k imagi virtuálního počítače založené na Windows**
1. Zadejte plně kvalifikovaný **název DNS virtuálního počítače** (například `MyVMName.Cloudapp.net`).
2. Zadejte hodnoty pro **uživatelské jméno** a **heslo**.

   ![Ověřování hesla Windows Image virtuálního počítače](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Spustit test certifikace

Poté, co jste zadali hodnoty parametrů pro vaši image virtuálního počítače v nástroji pro certifikaci, vybrat **Test připojení** zajistit platné připojení k vašemu virtuálnímu počítači. Po ověření připojení vyberte **Další** spuštění testu.  Po dokončení testu se zobrazí tabulku s výsledky testu (Pass/neprošel/upozornění).  Následující příklad ukazuje výsledky testu pro testovací virtuální počítač s Linuxem. 

![Výsledky testu certifikaci pro image virtuálního počítače s Linuxem](./media/publishvm_029.png)

Pokud selže některý z testů, vaše image je *není* certified. V takovém případě Seznamte se s požadavky a zprávy o selhání provedení požadovaných změn a opětovné spuštění testu. 

Po automatizovaný test, je potřeba poskytnout další informace o vaší image virtuálního počítače na **dotazník** obrazovky.  Obsahuje dvě karty, které je třeba provést.  **Obecné posouzení** karta obsahuje **True/False** otázky, které se, že **jádra přizpůsobení** obsahuje několik výběru a volného tvaru dotazy.  Dokončení dotazy na obou kartách a poté vyberte příkaz **Další**.

![Certifikační nástroj dotazník](./media/publishvm_030.png)

Na poslední obrazovce můžete zadat další informace, jako jsou informace o přístup SSH pro image virtuálního počítače s Linuxem a vysvětlení všechny neúspěšné vyhodnocení, pokud požadujete výjimky. 

A konečně, klikněte na tlačítko **Generovat sestavy** výsledky testů stáhnout a soubory protokolu pro prováděnou testovací případy navíc k odpovědi na dotazník. Uložte výsledky ve stejném kontejneru jako vaše virtuální pevné disky.

![Certifikace uložit výsledky testů](./media/publishvm_031.png)


## <a name="next-steps"></a>Další postup

Dále musíte [identifikátory URI (URI) vygenerovat pro každý virtuální pevný disk](./cpp-get-sas-uri.md) , které uvedete na webu Marketplace. 
