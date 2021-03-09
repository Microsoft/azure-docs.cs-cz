---
title: Sestavy Azure Backup e-mailu
description: Vytváření automatizovaných úloh pro příjem pravidelných sestav prostřednictvím e-mailu
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 8c18d4c7a3c7a9ba343296961fa9a44614366405
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510472"
---
# <a name="email-azure-backup-reports"></a>Sestavy Azure Backup e-mailu

Pomocí funkce **Sestava e-mailu** dostupné v sestavách Backup můžete vytvářet automatizované úlohy pro příjem pravidelných sestav prostřednictvím e-mailu. Tato funkce funguje tak, že se v prostředí Azure nasadí aplikace logiky, která se dotazuje na data z vybraných pracovních prostorů Log Analytics (LA) na základě zadaných vstupů. [Přečtěte si další informace o Logic Apps a jejich cenách](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>Začínáme

Chcete-li konfigurovat e-mailové úkoly prostřednictvím sestav zálohování, proveďte následující kroky:

1.  Přejděte na   >  **sestavy zálohování** centra záloh a klikněte na kartu **zpráva k odeslání e-mailu** .
2.  Vytvořte úlohu zadáním následujících informací:
    * **Podrobnosti o úloze** – název aplikace logiky, která se má vytvořit, a předplatné, skupinu prostředků a umístění, ve kterém se má vytvořit. Všimněte si, že aplikace logiky může dotazovat data napříč několika předplatnými, skupinami prostředků a umístěními (podle výběru v oddílu filtry sestav), ale vytvoří se v kontextu jednoho předplatného, skupiny prostředků a umístění.
    * **Data pro export** – karta, kterou chcete exportovat. Můžete buď vytvořit aplikaci s jedním úkolem na jednu kartu, nebo poslat e-mailem všechny karty pomocí jedné úlohy, a to tak, že vyberete možnost **všechny karty** .
    * **Možnosti e-mailu**: četnost e-mailů, ID e-mailů příjemce a předmět e-mailu.

    ![Karta e-mail](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  Po kliknutí na **Odeslat** a **Potvrdit** se vytvoří aplikace logiky. Aplikace logiky a přidružená připojení rozhraní API se vytvoří pomocí značky **UsedByBackupReports: true** pro snazší zjistitelnost. K úspěšnému spuštění aplikace logiky musíte provést jednorázový krok autorizace, jak je popsáno v následující části.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Autorizovat připojení k protokolům Azure Monitor a Office 365

Aplikace logiky používá konektor [azuremonitorlogs](https://docs.microsoft.com/connectors/azuremonitorlogs/) pro dotazování na pracovní prostory La a používá konektor [Office 365 Outlook](https://docs.microsoft.com/connectors/office365connector/) pro posílání e-mailů. Pro tyto dva konektory bude nutné provést jednorázovou autorizaci. 
 
Chcete-li provést autorizaci, postupujte podle následujících kroků:

1.  V Azure Portal přejděte na **Logic Apps** .
2.  Vyhledejte název aplikace logiky, kterou jste vytvořili, a přejděte k prostředku.

    ![Logic Apps](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  Klikněte na položku nabídky **připojení rozhraní API** .

    ![Připojení rozhraní API](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  Zobrazí se dvě připojení s formátem `<location>-azuremonitorlogs` a `<location>-office365` to je _eastus-azuremonitorlogs_ a _eastus-Office 365_.
5.  Přejděte ke každé z těchto připojení a vyberte položku nabídky **Upravit připojení rozhraní API** . Na obrazovce, která se zobrazí, vyberte **autorizovat** a po dokončení autorizace uložte připojení.

    ![Autorizovat připojení](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  Pokud chcete otestovat, jestli aplikace logiky funguje po autorizaci, můžete přejít zpátky do aplikace logiky, otevřít **Přehled** a vybrat **Spustit Trigger** v horním podokně a otestovat, jestli se e-mail úspěšně generuje.

## <a name="contents-of-the-email"></a>Obsah e-mailu

* Všechny grafy a grafy zobrazené na portálu jsou k dispozici jako vložený obsah v e-mailu.
* Mřížky zobrazené na portálu jsou k dispozici jako přílohy *. CSV v e-mailu.
* Data zobrazená v e-mailu používají všechny filtry na úrovni sestavy vybrané uživatelem v sestavě v době vytváření e-mailového úkolu.
* Filtry na úrovni karet, jako je **název instance zálohování**, **název zásady** atd., se nepoužívají. Jedinou výjimkou je mřížka **optimalizace uchovávání dat** na kartě **optimalizace** , kde se používají filtry pro **denní**, **týdenní**, **měsíční** a **roční** uchování RP.
* Časový rozsah a typ agregace (pro grafy) jsou založeny na výběru časového rozsahu uživatele v sestavách. Pokud je například časový rozsah výběru v rozmezí 60 dní (překládá se na týdenní typ agregace) a frekvence e-mailů je denně, obdrží každý den e-mail s grafy zahrnujícími data převzatými během posledních 60 dnů. data budou agregovaná na týdenní úrovni.

## <a name="troubleshooting-issues"></a>Řešení potíží

Pokud nepřijímáte e-maily podle očekávání i po úspěšném nasazení aplikace logiky, můžete při řešení potíží s konfigurací použít následující postup:

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>Scénář 1: příjem neúspěšného e-mailu ani chybové zprávy

* K tomuto problému může dojít, protože konektor Outlook API není autorizovaný. K autorizaci připojení použijte postup autorizace uvedený výše.

* K tomuto problému může dojít také v případě, že jste při vytváření aplikace logiky zadali nesprávného příjemce e-mailu. Pokud chcete ověřit, jestli se příjemce e-mailu zadal správně, můžete přejít do aplikace logiky v Azure Portal, otevřít návrháře aplikace logiky a vybrat e-mailový krok, abyste viděli, jestli se používají správná ID e-mailu.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>Scénář 2: příjem e-mailu s chybou oznamující, že se nepovedlo spustit aplikaci logiky a dokončit

Řešení tohoto problému:
1.  V Azure Portal přejděte do aplikace logiky.
2.  V dolní části obrazovky s **přehledem** se zobrazí část **historie spuštění** . Můžete otevřít nejnovější běh a zobrazit kroky v pracovním postupu, které selhaly. Možné příčiny:
    * **Konektor Azure Monitorch protokolů nebyl autorizován**: Chcete-li tento problém vyřešit, postupujte podle pokynů k autorizaci, jak je uvedeno výše.
    * **Chyba v dotazu La**: pro případ, že jste přizpůsobili aplikaci logiky s vlastními dotazy, může být chyba v některém z dotazů La příčinou selhání aplikace logiky. Můžete vybrat příslušný krok a zobrazit chybu, která způsobuje nesprávné spuštění dotazu.

Pokud potíže přetrvávají, obraťte se na podporu Microsoftu.

## <a name="next-steps"></a>Další kroky
[Další informace o sestavách zálohování](https://docs.microsoft.com/azure/backup/configure-reports)
