Teď, když přidáte trigger, s daty, který je generován aktivační událost zajímavé své doby něco udělat. Postupujte podle těchto kroků přidejte **SharePoint Online – vytvořit soubor** akce. Tato akce vytvoří soubor v Sharepointu Online pokaždé, když nové položky trigger spustí. 

Ke konfiguraci této akce, budete muset zadat následující informace. Jak tento údaj zadáte, si všimnete, že je snadno použitelný dat vygenerovaných vašimi aktivační událost jako vstup pro některé vlastnosti nového souboru:

| Vytvoření vlastnosti souboru | Popis |
| --- | --- |
| Adresa URL webu |Toto je adresa URL webu Sharepointu Online, kde chcete vytvořit nový soubor. Vyberte lokalitu ze seznamu uvedené. |
| Cesta ke složce |Je to složka (na adrese URL webu vybrali v předchozím kroku) umístění nového souboru. Vyhledejte a vyberte složku. |
| Název souboru |Toto je název souboru, který vytváří. |
| Obsah souboru |Obsah, který bude zapsán do souboru. |

1. Vyberte **+ nový krok** přidáte akci.  
   ![SharePoint online akce obrázku 1](./media/connectors-create-api-sharepointonline/action-1.png)  
2. Vyberte **přidat akci** odkaz. Tato otevře vyhledávací pole, kde můžete vyhledat jakoukoli akci jste chtěli provést. V tomto příkladu jsou akce Sharepointu zajímavé.    
   ![SharePoint online akce obrázek 2](./media/connectors-create-api-sharepointonline/action-2.png)    
3. Zadejte *sharepoint* hledání pro akce související s SharePoint.
4. Vyberte **SharePoint Online – vytvořit soubor** jako akce má být provedena.   **Poznámka:**: zobrazí výzva k autorizaci aplikace logiky pro přístup k účtu služby SharePoint, pokud jste zatím nevytvořili připojení k Sharepointu Online.    
   ![Online akce obrázků SharePoint 3](./media/connectors-create-api-sharepointonline/action-3.png)    
5. **Vytvořit soubor** ovládací prvek se otevře.   
   ![SharePoint online akce obrázek 4](./media/connectors-create-api-sharepointonline/action-4.png)     
6. Vyberte **adresa URL webu** a procházet a vyhledat lokalitu, ve kterém chcete vytvořit soubor.     
   ![SharePoint online akce obrázku 5](./media/connectors-create-api-sharepointonline/action-5.png)  
7. Vyberte **cesta ke složce** a procházením vyhledejte složku, kde budou umístěny do nového souboru.  
   ![SharePoint online akce obrázek 6](./media/connectors-create-api-sharepointonline/action-6.png)  
8. Vyberte **název_souboru** řídit a zadejte název souboru, kterou chcete vytvořit. Tady můžete zadat název souboru přímo nebo můžete použít některý z vlastnosti aktivační události, kterou jste vytvořili dříve. To provedete tak, že vyberete vlastnosti seznamu **vytvořené jako výstupy z když je vytvořena nová položka**. Tento seznam je pouze zobrazení po výběru **název_souboru** ovládacího prvku. V tomto názorný vybraná ID (ID nové položky seznamu) jako název souboru, který se vytvořil **SharePoint Online – vytvořit soubor** akce.    
   ![SharePoint online akce obrázek 7](./media/connectors-create-api-sharepointonline/action-7.png)  
9. Vyberte **obsah souboru** řídit a vložte obsah, který bude zapsán do souboru, který bude vytvořen. Obsah souboru Všimněte si, které můžete použít některou z vlastností aktivační události, kterou jste vytvořili dříve. Jednoduše vyberte ze seznamu uvedené vlastnosti. Alternativně můžete zadat **obsah souboru** text přímo do ovládacího prvku. V tomto příkladu jsem vybrali některé vlastnosti a přidat mezery a pomlčku každou vlastnost.        
   ![SharePoint online akce obrázek 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. Uložte změny do svého pracovního postupu  
11. Blahopřejeme, nyní máte plně funkční prostředek, který se aktivuje, když se do seznamu Sharepointu Online přidá nová položka. Aplikace se pak vytvoří soubor, pomocí některé z vlastností z nové položky seznamu.  Nyní ji můžete otestovat tím, že vytvoříte novou položku v Sharepointovém seznamu. 

