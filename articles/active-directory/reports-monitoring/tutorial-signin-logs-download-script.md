---
title: Kurz, jak stáhnout a použít skript pro přístup k signin protokoly | Dokumentace Microsoftu
description: Zjistěte, jak stáhnout a použít skript prostředí PowerShell pro přístup k protokolům signin.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4afe0c73-aee8-47f1-a6cb-2d71fd6719d1
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3be44af41de5c5115b1ddeaef79b64f26f8c8463
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911849"
---
# <a name="tutorial-how-to-download-and-use-a-script-to-access-sign-in-logs"></a>Kurz: Jak si stáhnout a použít skript, který přístup k protokolům přihlášení

Pokud chcete pracovat s daty aktivit přihlašování mimo web Azure Portal, můžete si je stáhnout. **Stáhnout** na webu Azure Portal se vytvoří soubor CSV nejnovější 5000 záznamů. Pokud potřebujete větší flexibilitu, například stáhnout více než 5000 záznamů najednou, nebo pro stahování protokolů v naplánovaných intervalech, můžete použít **skript** tlačítko vygenerovat skript Powershellu stáhnout data.

V tomto kurzu se dozvíte, jak se vygenerovat skript ke stažení všechny přihlášení protokoly za posledních 24 hodin a naplánovat jeho spuštění každý den. 

## <a name="prerequisites"></a>Požadavky

Potřebuješ

* Tenanta služby Azure Active Directory premium (P1/P2) licenci. Všimněte si, že pokud nemáte žádná data aktivity před upgradem, bude trvat několik dní daná data zobrazit v sestavách po upgradu na licenci premium. 
* Uživatel, který je v **globálního správce**, **správce zabezpečení**, **Čtenář zabezpečení** nebo **čtečky sestav** role pro příslušného tenanta. Každý uživatel můžete navíc použít vlastní přihlášení. 
* Pokud chcete spustit na počítači s Windows 10, staženého skriptu [nastavit modul Azure PowerShell a nastavte zásady spouštění](concept-sign-ins.md#running-the-script-on-a-windows-10-machine).

## <a name="tutorial"></a>Kurz

1. Přejděte [webu Azure portal](https://portal.azure.com) a vyberte svůj adresář.
2. Vyberte **Azure Active Directory** a vyberte **přihlášení** z **monitorování** oddílu. 
3. Použití **rozsah** filtr rozevíracího seznamu a vyberte **24 hodin** k získání dat z posledních 24 hodin. 
4. Vyberte **použít** a ověřte, že se filtr použije podle očekávání. 
5. Vyberte **skript** z hlavní nabídky se stáhnout skript prostředí Powershell s použitými filtry.

     ![Stáhněte si skript](./media/tutorial-signin-logs-download-script/download-script.png)
     
6. Otevřít **Plánovač úloh** aplikace na počítači s Windows a vyberte **vytvořit základní úkol**.
7. Zadejte název a popis pro úlohu a klikněte na tlačítko **Další**.
8. Vyberte **denní** přepínací tlačítko, které umožňují úloh a spouštět každý den zadejte počáteční datum a čas.
9. V nabídce Akce vyberte **spustit program** staženého skriptu a vyberte vyberte **Další**. 
10. Zkontrolujte naplánované úlohy a vyberte **Dokončit** vytvořte úkol.

     ![Vytvoření úkolu](./media/tutorial-signin-logs-download-script/create-task.png)

Nyní, bude úloha spouštění každý den a uložit záznamy přihlášení z posledních 24 hodin do souboru ve formátu **AAD_SignInReport_YYYYMMDD_HHMMSS.csv**. Můžete také upravit staženého skriptu prostředí PowerShell, ho uložit pod jiným názvem nebo chcete změnit počet sad záznamů načtených. 

## <a name="next-steps"></a>Další postup

* [Zásady uchovávání sestav služby Azure Active Directory](reference-reports-data-retention.md)
* [Začínáme s rozhraním API pro generování sestav v Azure Active Directory](concept-reporting-api.md)
* [Přístup k API pro vytváření sestav s certifikáty](tutorial-access-api-with-certificates.md)
