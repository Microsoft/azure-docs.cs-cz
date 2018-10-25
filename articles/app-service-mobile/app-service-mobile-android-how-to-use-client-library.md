---
title: Jak používat Azure Mobile Apps SDK pro Android | Dokumentace Microsoftu
description: Jak používat Azure Mobile Apps SDK pro Android
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: crdun
ms.openlocfilehash: a39ae42ba2344cb39318809e2f120e01a75344d7
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025782"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Jak používat Azure Mobile Apps SDK pro Android

Tato příručka ukazuje, jak používat s Androidem klientskou sadou SDK pro Mobile Apps k implementaci běžných scénářů, jako například:

* Dotazování na data (vložení, aktualizace nebo odstranění).
* ověřování.
* Zpracování chyb.
* Vlastní nastavení klienta.

Tato příručka se zaměřuje na sady Android SDK na straně klienta.  Další informace o serverové sady SDK pro Mobile Apps naleznete v tématu [pracovat s .NET back-end SDK][10] nebo [použití back-end Node.js SDK][11].

## <a name="reference-documentation"></a>Referenční dokumentace

Můžete najít [reference k rozhraní API Javadocs] [ 12] pro klientské knihovny Androidu na Githubu.

## <a name="supported-platforms"></a>Podporované platformy

Sada Azure Mobile Apps SDK pro Android podporuje rozhraní API úrovně 19 až 24 (KitKat prostřednictvím verzi Nougat) pro telefony a tablety provedení.  Ověřování, zejména využívá běžný postup webové rozhraní framework ke shromažďování přihlašovacích údajů.  Ověřování serveru toku nefunguje s malé formuláře faktor zařízení, jako jsou Watch.

## <a name="setup-and-prerequisites"></a>Instalace a požadavky

Dokončení [rychlý start Mobile Apps](app-service-mobile-android-get-started.md) kurzu.  Tato úloha se zajistí, že jsou splněné všechny požadavky pro Azure Mobile Apps pro vývoj.  Rychlý Start také vám pomůže nakonfigurovat svůj účet a vytvořte svůj první back-end mobilní aplikace.

Pokud se rozhodnete není pro absolvování tohoto kurzu rychlý start, proveďte následující úkoly:

* [Vytvoření back-end mobilní aplikace] [ 13] pomocí aplikace pro Android.
* V nástroji Android Studio [soubory sestavení Gradle aktualizace](#gradle-build).
* [Povolit oprávnění internet](#enable-internet).

### <a name="gradle-build"></a>Aktualizace souborem Gradle pro sestavení

Obě tyto hodnoty změnit **build.gradle** soubory:

1. Přidejte tento kód *projektu* úroveň **build.gradle** soubor uvnitř *buildscript* značky:

    ```text
    buildscript {
        repositories {
            jcenter()
        }
    }
    ```

2. Přidejte tento kód *modul app* úroveň **build.gradle** soubor uvnitř *závislosti* značky:

    ```text
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Nejnovější verze je aktuálně 3.4.0. Podporované verze jsou uvedeny [na panelu koše][14].

### <a name="enable-internet"></a>Povolit oprávnění internet

Přístup k Azure, aplikace musí mít povolené oprávnění INTERNET. Pokud ještě není povolené, přidejte následující řádek kódu, který vaše **AndroidManifest.xml** souboru:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Vytvořte připojení klienta

Azure Mobile Apps poskytuje čtyři funkce pro mobilní aplikace:

* Přístup k datům a Offline synchronizaci s mobilní aplikací služby Azure.
* Volání vlastních rozhraní API, napsané pomocí sady Azure Mobile Apps Server SDK.
* Ověřování pomocí služby Azure App Service ověřování a autorizace.
* Nabízená oznámení zaregistrovat pomocí Notification Hubs.

Každá z těchto funkcí nejprve potřeba, abyste vytvořili `MobileServiceClient` objektu.  Pouze jeden `MobileServiceClient` objekt by měl být vytvořen v rámci mobilního klienta (to znamená, že by měl být vzor s jedním prvkem).  Chcete-li vytvořit `MobileServiceClient` objektu:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

`<MobileAppUrl>` Je řetězec nebo objekt adresy URL, která odkazuje na vaše mobilní back-end.  Pokud používáte službu Azure App Service k hostování mobilních back-endu, zajistěte použijete zabezpečené `https://` verze adresy URL.

Klient také vyžaduje přístup k aktivitu nebo kontext - `this` parametr v příkladu.  Konstrukce MobileServiceClient se stane v rámci `onCreate()` metoda aktivity odkazuje `AndroidManifest.xml` souboru.

Jako osvědčený postup by měl abstraktní komunikaci mezi serverem do své vlastní třídy (singleton vzor).  V takovém případě je třeba předat aktivity v rámci konstruktoru na odpovídajícím způsobem nakonfigurovat službu.  Příklad:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Nyní můžete volat `AzureServiceAdapter.Initialize(this);` v `onCreate()` metoda hlavní činnost.  Všechny ostatní metody by potřebovali mít přístup k použití klienta `AzureServiceAdapter.getInstance();` k získání odkazu na adaptér služby.

## <a name="data-operations"></a>Operace s daty

Základní sady Azure Mobile Apps SDK je poskytnutí přístupu k datům uloženým v rámci SQL Azure na back-endu mobilní aplikace.  Můžete přístup k těmto datům pomocí silného typu třídy (upřednostňováno) nebo netypová dotazy (nedoporučuje se).  Hromadné Tato část se zabývá pomocí třídy silného typu.

### <a name="define-client-data-classes"></a>Definování datových tříd klienta

Pro přístup k datům z tabulek SQL Azure, definujte klienta datových tříd, které odpovídají na tabulky v back-endu mobilní aplikace. Příklady v tomto tématu předpokládají tabulku s názvem **MyDataTable**, který má následující sloupce:

* id
* text
* Dokončení

Odpovídající zadaný objekt na straně klienta se nachází v souboru s názvem **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Přidání metody getter a setter pro každé pole, které přidáte.  Pokud tabulka SQL Azure obsahuje více sloupců, by se do této třídy přidat odpovídající pole.  Například pokud objekt DTO (objekt pro přenos dat), měla prioritu sloupec celých čísel a potom může přidat tato pole, spolu s jeho metody getter a setter:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Další postup vytvoření dalších tabulek v váš back-end Mobile Apps naleznete v tématu [postupy: definování řadič tabulky][15] (.NET back-end) nebo [definovat tabulky pomocí dynamické schématu][16] (back-end Node.js).

Tabulku back-endu Azure Mobile Apps definuje pět zvláštní pole čtyři z nich jsou dostupné klientům:

* `String id`: Globálně jedinečné ID záznamu.  Jako osvědčený postup, ujistěte se, id řetězcové vyjádření [UUID] [ 17] objektu.
* `DateTimeOffset updatedAt`: Datum/čas poslední aktualizace.  Pole updatedAt nastavit server a by nikdy nastavit váš klientský kód.
* `DateTimeOffset createdAt`: Data a času, který byl vytvořen objekt.  Pole createdAt nastavit server a by nikdy nastavit váš klientský kód.
* `byte[] version`: Obvykle reprezentovaná jako řetězec, verze je také nastavena na serveru.
* `boolean deleted`: Označuje, že má záznam odstranit ale ještě nebyl vymazán.  Nepoužívejte `deleted` jako vlastnost ve své třídě.

Pole `id` je povinné.  `updatedAt` Pole a `version` pole se používají pro offline synchronizaci (pro přírůstkové synchronizace a ke konfliktu rozlišení v uvedeném pořadí).  `createdAt` Pole je referenční pole a není použito klientem.  Názvy jsou "napříč přenosu" názvy vlastností a nejsou měnitelné.  Však můžete vytvořit mapování mezi objekt a názvy "napříč přenosu" pomocí [gson] [ 3] knihovny.  Příklad:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Vytvořit odkaz na tabulku

Chcete-li získat přístup k tabulce, nejprve vytvořte [MobileServiceTable] [ 8] objektu voláním **jít** metodu na [MobileServiceClient] [9].  Tato metoda má dvě přetížení:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

V následujícím kódu **mClient** je odkaz na objekt MobileServiceClient.  První přetížení se používá název třídy a název tabulky jsou stejné, kde je použit v tomto rychlém startu:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Druhé přetížení se používá, když se liší od názvu třídy název tabulky: první parametr je název tabulky.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Dotaz na tabulku back-endu

Nejprve získejte odkaz na tabulku.  Pak spustí dotaz na odkaz na tabulku.  Dotaz, který je kombinací:

* A `.where()` [klauzuli filtru](#filtering).
* `.orderBy()` [Klauzule ordering](#sorting).
* A `.select()` [klauzuli výběru pole](#selection).
* A `.skip()` a `.top()` pro [stránkovaných výsledků](#paging).

Klauzule musí být seřazen podle předchozího.

### <a name="filter"></a> Filtrování výsledků

Je obecný formulář dotazu:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

V předchozím příkladu vrátí všechny výsledky (až do maximální velikost stránky nastavit server).  `.execute()` Metoda provede dotaz na back-endu.  Dotaz je převedena na [OData v3] [ 19] Dotázat se před přenosem do back-endu Mobile Apps.  Po obdržení back-end Mobile Apps převede dotaz na příkazu SQL před spuštěním na instanci SQL Azure.  Protože nějakou dobu trvá síťové aktivity `.execute()` metoda vrátí hodnotu [ `ListenableFuture<E>` ] [ 18].

### <a name="filtering"></a>Filtr vrátil data

Spuštění následujícího dotazu vrátí všechny položky z **ToDoItem** tabulky where **kompletní** rovná **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** je odkaz na tabulku mobilních služeb, který jsme vytvořili dříve.

Definujte filtr pomocí **kde** volání metody na odkaz na tabulku. **Kde** následuje metoda **pole** metoda následovaný metodu, která určuje logický predikátu. Je to možné predikátu metody patří **eq** (rovná se), **ne** (není rovno) **gt** (větší než) **ge** (větší než nebo rovna hodnotě), **lt** (menší než), **le** (menší než nebo rovno). Tyto metody umožňují porovnat pole číslo a řetězec k určitým hodnotám.

Můžete filtrovat podle data. Následující metody umožňují porovnání pole pro datum celého nebo části datum: **rok**, **měsíc**, **den**, **hodinu**,  **minuta**, a **druhý**. Následující příklad přidá filtr pro položky jehož *termín splnění* rovná 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Následující metody podporují složité filtry na pole řetězců: **startsWith**, **endsWith**, **concat**, **podřetězec**, **indexOf**, **nahradit**, **toLower**, **toUpper**, **trim**, a **délku** . Následující příklad filtry pro tabulku řádky, ve kterých *text* začíná "PRI0."

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Jsou podporovány následující metody operátor na pole s počtem: **přidat**, **sub**, **mul**, **div**, **mod**, **floor**, **horní mez**, a **ZAOKROUHLIT**. Následující příklad filtry pro tabulku řádky, ve kterých **doba trvání** sudé číslo.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Predikáty můžete kombinovat s těmito metodami logické: **a**, **nebo** a **není**. Následující příklad kombinuje dvě z předchozích příkladů.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Skupiny a vnořených logické operátory:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Podrobnější informace a příklady, filtrování, najdete v článku [zkoumání bohatost model dotazování Android klienta][20].

### <a name="sorting"></a>Řazení vrátil data

Následující kód vrátí všechny položky z tabulky **ToDoItems** seřazeno vzestupně podle *text* pole. *mToDoTable* se odkaz na back-endovou tabulku, kterou jste vytvořili dříve:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

První parametr **orderBy** metodou je stejný jako název pole, na kterém se má seřadit řetězce. Druhý parametr používá **QueryOrder** výčet k určení, jestli se má seřadit vzestupně nebo sestupně.  Pokud provádíte filtrování pomocí ***kde*** metody ***kde*** před je nutné volat metodu ***orderBy*** – metoda.

### <a name="selection"></a>Vyberte sloupce zaškrtnutím

Následující kód ukazuje, jak vrátit všechny položky z tabulky **ToDoItems**, ale zobrazuje jenom **kompletní** a **text** pole. **mToDoTable** se odkaz na back-endovou tabulku, kterou jsme vytvořili dříve.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Parametry vyberte funkce jsou řetězcové názvy sloupců v tabulce, které chcete vrátit.  **Vyberte** metoda musí postupovat podle metody, jako je **kde** a **orderBy**. Může být následován stránkovací metody, jako je **přeskočit** a **horní**.

### <a name="paging"></a>Vrácení dat na stránkách

Data jsou **vždy** vráceny na stránkách.  Server je nastavena maximální počet vrácených záznamů.  Pokud klient požaduje více záznamů, server vrátí maximální počet záznamů.  Výchozí maximální velikost stránky na serveru je 50 záznamů.

První příklad ukazuje, jak vybrat prvních pět položek z tabulky. Dotaz vrátí položky z tabulky **ToDoItems**. **mToDoTable** se odkaz na back-endovou tabulku, kterou jste vytvořili dříve:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Tady je dotaz, který přeskočí prvních pět položek a potom vrátí další pěti:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Pokud chcete získat všechny záznamy v tabulce, implementujte kód a iterovat všechny stránky:

```java
List<MyDataModel> results = new List<MyDataModel>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Žádost o pro všechny záznamy pomocí této metody vytvoří minimálně dva požadavky do back-endu Mobile Apps.

> [!TIP]
> Volba velikosti pravá stránka je rovnováhu mezi využití paměti při žádosti se děje, využití šířky pásma a zpoždění při přijímání dat úplně.  Výchozí hodnota (50 záznamů) je vhodný pro všechna zařízení.  Pokud provozujete výhradně na větší paměťová zařízení, zvýšit až 500.  Zjistili, který zvyšuje velikost stránky nad rámec 500 záznamů výsledků v zpožděním a velké paměti problémy.

### <a name="chaining"></a>Postupy: řetězení metody dotazů

Metody používané v dotazy na back-endu tabulky mohou být spojeny. Řetězení metody dotazu, můžete vybrat konkrétní sloupcích filtrované řádky, které jsou seřazené a stránkovaného fondu. Můžete vytvořit komplexní logické filtry.  Každá metoda dotaz vrací objekt dotazu. Chcete-li ukončit řadu metod a skutečně spusťte dotaz, zavolejte **provést** metody. Příklad:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Metody zřetězených dotazů musí být uspořádaná následujícím způsobem:

1. Filtrování (**kde**) metody.
2. Řazení (**orderBy**) metody.
3. Výběr (**vyberte**) metody.
4. stránkování (**přeskočit** a **horní**) metody.

## <a name="binding"></a>Vytvoření vazby dat na uživatelské rozhraní

Vytváření datových vazeb zahrnuje tři komponenty:

* Zdroj dat
* Rozložení obrazovky
* Adaptér, který spojuje dvě.

V našem ukázkovém kódu jsme vrátit data z tabulky SQL Azure Mobile Apps **ToDoItem** na pole. Tato aktivita je běžný vzor pro data aplikací.  Databázové dotazy často vrátit sadu řádků, které klient získá ze seznamu nebo pole. V tomto příkladu je pole zdroje dat.  Kód určuje rozložení obrazovky, která definuje zobrazení data, která se zobrazí na zařízení.  Dva jsou vázána spolu s adaptér, který tento kód je rozšířením sady **ArrayAdapter&lt;ToDoItem&gt;**  třídy.

#### <a name="layout"></a>Definování rozložení

Rozložení je definováno více fragmentů kódu XML. Zadaný existující rozložení, následující kód představuje **ListView** chceme naplnit pomocí našich dat serveru.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

V předchozím kódu *listitem* atribut určuje id rozložení pro jednotlivé řádek v seznamu. Tento kód určuje zaškrtávací políčko a příslušný text a získá vytvořit jednou pro každou položku v seznamu. Toto rozložení nezobrazí **id** pole a složitější rozložení, zadejte další pole v zobrazení. Tento kód je v **row_list_to_do.xml** souboru.

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>Definování adaptéru
Vzhledem k tomu, že je zdroj dat naše zobrazení pole **ToDoItem**, jsme podtřídy naše adaptér ze **ArrayAdapter&lt;ToDoItem&gt;**  třídy. Tato podtřídy vytvoří zobrazení pro každý **ToDoItem** pomocí **row_list_to_do** rozložení.  V našem kódu definujeme následující třídy, která je rozšířením **ArrayAdapter&lt;E&gt;**  třídy:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Přepsat adaptéry **getView** metody. Příklad:

```
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

Vytvoření instance této třídy v naší činnosti následujícím způsobem:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Druhý parametr konstruktoru ToDoItemAdapter je odkaz na rozložení. Nyní jsme lze vytvořit instanci **ListView** a přiřaďte adaptér, který má **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Použít adaptér, který má vazbu na uživatelské rozhraní

Nyní jste připraveni používat datové vazby. Následující kód ukazuje, jak získat položky v tabulce a vyplní místní adaptér vrácených položek.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Kdykoli upravíte volání **ToDoItem** tabulky. Protože změny mít na základě záznamu podle, zpracovávat jeden řádek místo kolekce. Při vložení položky volání **přidat** metodu na adaptér; při odstraňování, volání **odebrat** metoda.

Kompletní příklad v lze najít [projekt rychlý start pro Android][21].

## <a name="inserting"></a>Vložit data do back-endu

Vytvoření instance instance *ToDoItem* třídy a nastavit jeho vlastnosti.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Pak pomocí **insert()** k vložení objektu:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Odpovídá vrácenou entitu data vložená do back-endovou tabulku zahrnuté ID a jiné hodnoty (například `createdAt`, `updatedAt`, a `version` pole) nastavena na back-endu.

Mobile Apps tabulky vyžadují sloupec primárního klíče s názvem **id**. Tento sloupec musí být řetězec. Výchozí hodnota ve sloupci ID je identifikátor GUID.  Můžete zadat další jedinečné hodnoty, jako je například e-mailové adresy nebo uživatelských jmen. Když pro vložený záznam není zadaná řetězcová hodnota Identifikátor, back-endu vygeneruje nový identifikátor GUID.

Hodnota ID řetězce poskytuje následující výhody:

* ID je generovat přitom latence do databáze.
* Záznamy se snadněji sloučení z různých tabulek nebo databází.
* ID hodnoty lépe integrovat aplikace logiky.

Řetězec ID hodnoty jsou **povinné** pro podporu offline synchronizace.  Id nelze změnit, jakmile je uložená v databázi back-endu.

## <a name="updating"></a>Aktualizace dat v mobilní aplikaci

Pokud chcete aktualizovat data v tabulce, předejte nový objekt, který **update()** metoda.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

V tomto příkladu *položky* je odkaz na řádek v *ToDoItem* tabulku, která má určitá některé změny.  Řádek se stejným **id** se aktualizuje.

## <a name="deleting"></a>Odstranit data v mobilní aplikaci

Následující kód ukazuje, jak odstranit data z tabulky tak, že zadáte datový objekt.

```java
mToDoTable
    .delete(item);
```

Položku můžete odstranit tak, že zadáte **id** pole řádku, který chcete odstranit.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Vyhledat konkrétní položky podle Id

Vyhledat položku s určitým **id** pole **lookUp()** metody:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Postupy: práce s daty bez typu

Netypové programovací model poskytuje přesnou kontrolu nad serializace JSON.  Zde jsou uvedeny některé obvyklé scénáře, kde můžete chtít použít netypové programovací model. Například, pokud tabulka back-end obsahuje mnoho sloupců a potřebujete odkazovat na podmnožinu sloupců.  Zadaný model vyžaduje, abyste definujte všechny řádky, které jsou definovány v back-endu Mobile Apps ve své třídě data.  Většinu volání rozhraní API pro přístup k datům jsou podobné typy programovací volání. Hlavní rozdíl spočívá v tom, že v netypové modelu můžete vyvolávat metody v **MobileServiceJsonTable** objektu, nikoli **MobileServiceTable** objektu.

### <a name="json_instance"></a>Vytvoření instance netypové tabulky

Podobně jako na zadaný model, je začít nastavením odkaz na tabulku, ale v tomto případě jde **MobileServicesJsonTable** objektu. Získat odkaz pomocí volání **jít** metodu na instanci klienta:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Po vytvoření instance **MobileServiceJsonTable**, má téměř stejné rozhraní API k dispozici jako s typem programovací model. V některých případech může trvat metody netypový parametr místo typu parametru.

### <a name="json_insert"></a>Vložit do netypové tabulky
Následující kód ukazuje, jak provést vložení. Prvním krokem je vytvoření [JsonObject][1], který je součástí [gson] [ 3] knihovny.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Potom použijte **insert()** netypové objekt vložit do tabulky.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Pokud je potřeba získat ID vloženého objektu, použijte **getAsJsonPrimitive()** metody.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Odstranit z netypového tabulky.
Následující kód ukazuje, jak odstranit instance, v tomto případě stejné instance **JsonObject** , který byl vytvořen v předchozího *vložit* příklad. Kód je stejný jako s typem případ, ale tato metoda má jiný podpis, protože odkazuje na **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Instance můžete odstranit také přímo pomocí jeho ID:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Vrátí všechny řádky z tabulky bez typu
Následující kód ukazuje, jak načíst celou tabulku. Vzhledem k tomu, že používáte tabulku JSON, můžete selektivně načíst jenom některé sloupce v tabulce.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

Stejnou sadu filtrování, filtrování a stránkování metody, které jsou k dispozici pro typy modelu jsou k dispozici pro netypový kód modelu.

## <a name="offline-sync"></a>Implementace Offline synchronizace

Azure Mobile Apps Client SDK také implementuje offline synchronizace dat s využitím databáze SQLite k uložení kopie dat serveru místně.  Operace provedené na offline tabulce nevyžadují, aby mobilní připojení k práci.  Offline synchronizace pomáhá při odolnost a výkon za cenu mnohem složitější logiku pro případ řešení konfliktů.  Azure Mobile Apps Client SDK implementuje následující funkce:

* Přírůstková synchronizace: Pouze aktualizované a nové záznamy se stahují, ukládají se využití šířky pásma a paměti.
* Optimistického řízení souběžnosti: Operace se předpokládá, že proběhla úspěšně.  Řešení konfliktů je odloženo, dokud se aktualizace prováděly na serveru.
* Řešení konfliktů: Sada SDK rozpozná konfliktní změny byly provedeny na serveru a poskytuje zachytávání k upozornění uživatele.
* Obnovitelné odstranění: Odstraněné záznamy jsou označeny odstraněné, což jiná zařízení k aktualizaci jejich offline mezipaměti.

### <a name="initialize-offline-sync"></a>Inicializovat Offline synchronizace

Každá tabulka v režimu offline, musí být definován v offline mezipaměti před použitím.  Definice tabulky za normálních okolností se provádí ihned po vytvoření klienta:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Získání odkazu na tabulku mezipaměti v režimu Offline

Pro tabulku online používáte `.getTable()`.  Pro tabulku v režimu offline použijte `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Všechny metody, které jsou k dispozici pro online tabulky (včetně filtrování, řazení, stránkování, vkládání dat, aktualizace dat a odstranění dat) fungovat stejně dobře u tabulek se online i offline.

### <a name="synchronize-the-local-offline-cache"></a>Synchronizovat místní mezipaměti v režimu Offline

Synchronizace se v ovládacím prvku vaší aplikace.  Tady je příklad metoda synchronizace:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Pokud název dotazu je k dispozici na `.pull(query, queryname)` metoda pak Přírůstková synchronizace slouží k vrátí pouze záznamy, které byly vytvořené nebo změněné od poslední úspěšně dokončit o přijetí změn.

### <a name="handle-conflicts-during-offline-synchronization"></a>Řešení konfliktů při Offline synchronizaci

Pokud dojde ke konfliktu během `.push()` operace, `MobileServiceConflictException` je vyvolána výjimka.   Server vydal položky se vloží do výjimky a je možné načíst podle `.getItem()` na výjimku.  Upravte nasdílení změn pomocí volání na objekt MobileServiceSyncContext následující položky:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Jakmile se všechny konflikty jsou označená podle potřeby, volání `.push()` znovu a vyřešit všechny konflikty.

## <a name="custom-api"></a>Volání vlastních rozhraní API

Vlastní rozhraní API vám umožní definovat vlastní koncové body, které zveřejňují funkce serveru, které nejsou mapování pro vložení, aktualizaci, odstranění nebo operace čtení. Pomocí vlastního rozhraní API může mít větší kontrolu nad zasílání zpráv, včetně čtení a nastavení hlavičky HTTP zpráv a definování formátu těla zprávy kromě formátu JSON.

Z Androidu klienta, můžete volat **invokeApi** metoda k volání vlastního koncového bodu rozhraní API. Následující příklad ukazuje, jak volat koncový bod rozhraní API s názvem **completeAll**, který vrátí třídu kolekce s názvem **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

**InvokeApi** metoda je volána na straně klienta, který odešle požadavek POST do nové vlastní rozhraní API. Výsledek vrácený z vlastního rozhraní API se zobrazí v dialogovém okně zpráva, jako jsou nějaké chyby. Další verze **invokeApi** můžete volitelně objektu v textu požadavku, zadejte metodu HTTP a odesílají parametry dotazu s požadavkem. Netypová verzích **invokeApi** jsou k dispozici také.

## <a name="authentication"></a>Přidání ověřování do aplikace

Podrobné kurzy již popisují způsob přidání těchto funkcí.

App Service podporuje [ověřování uživatelů aplikace](app-service-mobile-android-get-started-users.md) pomocí různých externích zprostředkovatelů identity: Facebook, Google, Microsoft Account, Twitter a Azure Active Directory. Můžete nastavit oprávnění pro tabulky, pokud chcete omezit přístup pro určité operace pouze ověřeným uživatelům. Identity ověřeného uživatele můžete také použít k implementaci autorizační pravidla v back-endu.

Jsou podporovány dvě toky ověřování: **server** toku a **klienta** toku. Tok server poskytuje nejjednodušší prostředí pro ověřování, spoléhá na webové rozhraní poskytovatele identity.  Žádné další sady SDK je potřeba implementovat tok ověřování serveru. Tok ověřování serveru neposkytuje hluboká integrace do mobilních zařízení a doporučuje se jen pro testování konceptu scénáře.

Tok klienta umožňuje hlubší integraci s funkcemi konkrétní zařízení, jako je jednotné přihlašování se spoléhá na SDK od poskytovatele identity.  Například můžete integrovat sadu SDK Facebooku do vaší aplikace.  Mobilního klienta Zamění do aplikace pro Facebook a potvrdí vaše přihlašování před přechodem do mobilní aplikace.

Povolení ověřování v aplikaci je potřeba provést čtyři kroky:

* Registrace aplikace pro ověřování pomocí zprostředkovatele identity.
* Konfigurace back-endu služby App Service.
* Omezte oprávnění tabulky pro ověřeného uživatele pouze na back-endu služby App Service.
* Ověřovací kód přidejte do své aplikace.

Můžete nastavit oprávnění pro tabulky, pokud chcete omezit přístup pro určité operace pouze ověřeným uživatelům. Identifikátor SID ověřeného uživatele můžete použít také k úpravě požadavky.  Další informace najdete v tématu [Začínáme s ověřováním] a v dokumentaci k serveru SDK postupy.

### <a name="caching"></a>Ověřování: Tok serveru

Následující kód spustí proces serveru toku přihlášení pomocí zprostředkovatele Google.  Další konfigurace se vyžaduje kvůli požadavkům na zabezpečení pro zprostředkovatele Google:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Kromě toho přidejte následující metodu do hlavní třída aktivit:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

`GOOGLE_LOGIN_REQUEST_CODE` Podle vašeho hlavního aktivita se používá pro `login()` – metoda a v rámci `onActivityResult()` metody.  Můžete použít libovolné jedinečné číslo, tak dlouho, dokud se používá stejné číslo v rámci `login()` metoda a `onActivityResult()` metoda.  Pokud abstrahování kódu klienta do služby adaptér (jak je uvedeno výše), byste měli volat metody odpovídající na adaptéru služby.

Budete potřebovat ke konfiguraci projektu pro customtabs.  Nejprve zadejte adresu URL přesměrování.  Přidejte následující fragment kódu a `AndroidManifest.xml`:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Přidat **redirectUriScheme** k `build.gradle` souboru pro vaši aplikaci:

```text
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Nakonec přidejte `com.android.support:customtabs:23.0.1` do seznamu závislosti `build.gradle` souboru:

```text
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.google.code.gson:gson:2.3'
    compile 'com.google.guava:guava:18.0'
    compile 'com.android.support:customtabs:23.0.1'
    compile 'com.squareup.okhttp:okhttp:2.5.0'
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Získat ID přihlášeného uživatele **MobileServiceUser** pomocí **getUserId** metoda. Příklad použití termínu k volání asynchronního rozhraní API pro přihlášení, naleznete v tématu [Začínáme s ověřováním].

> [!WARNING]
> Schéma adresy URL uvedené rozlišuje velká a malá písmena.  Ujistěte se, že všechny výskyty `{url_scheme_of_you_app}` rozlišovat velikost písmen.

### <a name="caching"></a>Tokeny ověřování mezipaměti

Ukládání do mezipaměti ověřovacích tokenů vyžaduje, abyste pro uložení ID uživatele a ověřovací token místně na zařízení. Při příštím spuštění aplikace, zkontrolujte mezipaměti, a pokud tyto hodnoty jsou k dispozici, můžete přeskočit protokolu v postupu a dosazení klienta s těmito daty. Ale tato data jsou citlivá a by měla být uložena v případě, že telefon odcizen šifrována pro bezpečnost.  Zobrazí se kompletní příklad toho, jak do mezipaměti ověřovacích tokenů v [mezipaměti ověřování tokenů části][7].

Při pokusu o použití tokenu vypršela platnost, se zobrazí *zobrazuje chyba 401 Neautorizováno* odpovědi. Můžete zpracovávat chyby s ověřováním pomocí filtrů.  Filtry zachycení požadavků na back-endu služby App Service. Kód filtru testuje odpovědi na 401, spustí proces přihlašování a potom pokračuje v žádosti, která vygenerovala 401.

### <a name="refresh"></a>Použít obnovovací tokeny

Token vrácený ověřování pomocí služby Azure App Service a autorizace má definovaný životnosti jednu hodinu.  Po uplynutí této doby nutné donutit uživatele.  Pokud používáte s dlouhým poločasem rozpadu token, který jste obdrželi prostřednictvím ověřování toku na straně klienta a pak můžete donutit s Azure App Service ověřování a autorizace pomocí stejného tokenu.  Další služby Azure App Service token generuje s použitím nové životnosti.

Budete taky moct registrovat poskytovatele za účelem použití aktualizace tokenů.  Aktualizovat Token není vždy k dispozici.  Je vyžadována další konfigurace:

* Pro **Azure Active Directory**, nakonfigurujte tajný kód klienta pro aplikaci Azure Active Directory.  Zadejte tajný kód klienta ve službě Azure App Service při konfiguraci ověřování služby Azure Active Directory.  Při volání metody `.login()`, předejte `response_type=code id_token` jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Pro **Google**, předejte `access_type=offline` jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Pro **Account Microsoft**, vyberte `wl.offline_access` oboru.

Chcete-li aktualizovat token, zavolejte `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Jako nejlepší postup vytvořte filtr, který zjistí 401 odpověď ze serveru a pokusí se obnovovací token uživatele.

## <a name="log-in-with-client-flow-authentication"></a>Přihlaste se pomocí ověření toku na straně klienta

Obecný proces přihlášení pomocí ověřování toku na straně klienta vypadá takto:

* Konfigurace ověřování pomocí služby Azure App Service a autorizaci stejně jako server tok ověřování.
* Integrace SDK k vytvoření přístupového tokenu pro ověření zprostředkovatele ověřování.
* Volání `.login()` metodu následujícím způsobem:

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Nahradit `onSuccess()` metodu cokoli, co kód chcete použít na úspěšném přihlášení.  `{provider}` Řetězec je neplatný poskytovatel: **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount**, nebo **twitter**.  Pokud jste implementovali vlastní ověřování, můžete také použít vlastní ověřovací značka zprostředkovatele.

### <a name="adal"></a>Ověřování uživatelů pomocí Active Directory Authentication Library (ADAL)

Můžete používat Active Directory Authentication Library (ADAL) pro přihlášení uživatelů do vaší aplikace pomocí Azure Active Directory. Použití toku přihlášení klienta je často vhodnější než použít `loginAsync()` metody, protože obsahuje více přirozený chování uživatelského prostředí a umožňuje další přizpůsobení.

1. Konfigurace back-endu mobilní aplikace pro přihlášení k AAD pomocí následujících [konfigurace služby App Service pro přihlášení služby Active Directory] [ 22] kurzu. Ujistěte se, že k dokončení volitelný krok registrace nativní klientské aplikace.
2. Nainstalujte knihovnu ADAL pomocí úpravy souboru build.gradle zahrnout následující definice:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

1. Přidejte následující kód do vaší aplikace a nahrazení následující:

* Nahraďte **INSERT-AUTORITY-KORENOVA** s názvem tenanta, ve kterém jste zřídili vaší aplikace. Formát by měl být https://login.microsoftonline.com/contoso.onmicrosoft.com.
* Nahraďte **INSERT-RESOURCE-ID – TADY** s ID klienta pro back-endu mobilní aplikace. Můžete získat ID klienta z **Upřesnit** kartu **nastavení služby Azure Active Directory** na portálu.
* Nahraďte **vložit klienta ID TADY** s ID klienta, který jste zkopírovali z nativní klientskou aplikaci.
* Nahraďte **vložení – PŘESMĚROVÁNÍ-URI-TADY** s vaší lokality */.auth/login/done* koncový bod, používat schéma HTTPS. Tato hodnota by měl být podobný *https://contoso.azurewebsites.net/.auth/login/done*.

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>Upravit komunikaci klient Server

Připojení klienta je obvykle základní připojení protokolu HTTP pomocí základního HTTP knihovny součástí sady Android SDK.  Tady je několik důvodů, proč byste měli změnit:

* Budete chtít použít alternativní knihovnu HTTP upravit vypršení časového limitu.
* Chcete poskytovat indikátor průběhu.
* Chcete přidat vlastní hlavičku pro podporu funkcí správy rozhraní API.
* Chcete zachytit neúspěšnou odpověď, a proto, že můžete implementovat opětovné ověření.
* Chcete protokolovat požadavky na back-endu do služby analýzy.

### <a name="using-an-alternate-http-library"></a>Použití alternativní knihovny HTTP

Volání `.setAndroidHttpClientFactory()` metoda ihned po vytvoření odkazu na klienta.  Chcete-li například nastavit časový limit připojení na 60 sekund (namísto výchozí hodnota 10 sekund):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClinet();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementovat filtr průběh

Zachycení každého požadavku můžete implementovat pomocí implementace `ServiceFilter`.  Následující příklad aktualizuje indikátor průběhu předem vytvořené:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Tento filtr je možné připojit klienta následujícím způsobem:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Přizpůsobení záhlaví požadavku

Pomocí následujících `ServiceFilter` a připojit filtr stejným způsobem jako `ProgressFilter`:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Konfigurovat automatické serializace

Můžete určit, které platí pro každý sloupec s použitím strategie převodu [gson] [ 3] rozhraní API. Klientské knihovny Androidu používá [gson] [ 3] na pozadí a serializovat objekty Java do formátu JSON data předtím, než se odešlou do služby Azure App Service.  Následující kód používá **setFieldNamingStrategy()** metody nastavte strategie. Tento příklad odstraní počáteční znak ("m") a potom malé další znak, pro každý název pole. Například jej by proměnit "střední" "id".  Strategie převodu na tak snížit potřeba implementovat `SerializedName()` poznámky na většina polí.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStategy)
);
```

Tento kód je nutné provést před vytvořením odkazu mobilního klienta pomocí **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Začínáme s ověřováním]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: http://www.odata.org/documentation/odata-version-3-0/
[20]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html
