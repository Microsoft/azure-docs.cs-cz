---
title: Prozkoumejte službu Azure Stream Analytics pomocí sady Visual Studio Code (Preview)
description: Tento článek ukazuje, jak exportovat úlohy Azure Stream Analytics na místní projekt seznamu úloh a zobrazení úloh entity a vytvořit kanál CI/CD pro vaši úlohu Stream Analytics.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: conceptual
ms.openlocfilehash: 640a81fcb94194e2e907e9339f016bd35e279fdd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079207"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Prozkoumejte službu Azure Stream Analytics pomocí sady Visual Studio Code (Preview)

Azure Stream Analytics pro Visual Studio Code příponou vývojářům poskytuje zjednodušené prostředí pro správu svých úloh Stream Analytics. Rozšíření Azure Stream Analytics vám umožní:

- [Vytvoření](quick-create-vs-code.md), spuštění a zastavení úlohy
- Exportovat stávající úlohy do místní projekt
- Spouštění dotazů místně
- Nastavení kanálu CI/CD

## <a name="export-a-job-to-a-local-project"></a>Exportovat úlohy do místní projekt

Exportovat úlohu do místní projekt, vyhledejte úlohu chcete exportovat **Průzkumníku Stream Analytics** ve Visual Studio code. Potom vyberte složku pro váš projekt. Projekt se exportují do vámi vybrané složky. proto můžete nadále spravovat úlohy z Visual Studio Code. Další informace o použití ke správě úlohy Stream Analytics Visual Studio Code najdete v tématu Visual Studio Code [rychlý Start](quick-create-vs-code.md).

![Exportovat úlohy Azure Stream Analytics ve Visual Studio Code](./media/vs-code-how-to/export-job.png)

## <a name="run-queries-locally"></a>Spouštění dotazů místně

Rozšíření Azure Stream Analytics pro Visual Studio Code můžete použít k otestování vaší úlohy Stream Analytics místně s ukázkovými daty.

1. Po vytvoření vaší úlohy Stream Analytics, použijte **Ctrl + Shift + P** otevřete paletu příkazu. Zadejte a vyberte **Azure Stream Analytics: Přidat vstup**.

    ![Přidat vstup Azure Stream Analytics ve Visual Studio code](./media/vs-code-how-to/add-input.png)

2. Vyberte **místní vstup**.

    ![Přidat místní vstup Azure Stream Analytics ve Visual Studio code](./media/vs-code-how-to/add-local-input.png)

3. Vyberte **+ nový místní vstup**.

    ![Přidat nové místní ASA vstup v aplikaci Visual Studio code](./media/vs-code-how-to/add-new-local-input.png)

4. Zadejte stejný vstupní alias, který jste použili v dotazu.

    ![Přidejte nový místní vstupní alias Azure Stream Analytics](./media/vs-code-how-to/new-local-input-alias.png)

5. V **LocalInput_DefaultLocalStream.json** soubor, zadejte cestu k souboru, kde se nachází vaše data z místního souboru.

    ![Zadejte cestu k místnímu souboru v sadě Visual Studio](./media/vs-code-how-to/local-file-path.png)

6. Vraťte se do editoru dotazů a vyberte **místní spuštění**.

    ![Vyberte možnost spustit místně v editoru dotazů](./media/vs-code-how-to/run-locally.png)

## <a name="set-up-a-cicd-pipeline"></a>Nastavení kanálu CI/CD

Můžete povolit průběžnou integraci a nasazování pro úlohy Azure Stream Analytics pomocí **nástroje Azure Stream Analytics cicd** balíčku NPM. Balíček NPM poskytuje nástroje pro automatické nasazení projekty Stream Analytics Visual Studio Code. Můžete použít ve Windows, macOS a Linuxu bez instalace Visual Studio Code.

Jakmile budete mít [stáhli balíček](https://usqldownload.blob.core.windows.net/ext/asa/asa-cicd-0.0.1-preview-beta.tar), použijte následující příkaz pro výstup šablony Azure Resource Manageru. Pokud **outputPath** neurčíte, šablony se umístí do **nasadit** ve složce projektu **bin** složky.

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

## <a name="next-steps"></a>Další postup

* [Vytvořit úlohu Azure Stream Analytics cloudu ve Visual Studio Code (Preview)](quick-create-vs-code.md)