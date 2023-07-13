---
layout: post
---

# Language Test



``` sql
/*
 *		TECH 60701 -- Technologies de l'intelligence d'affaires
 *					HEC Montréal
 *		Travail Pratique 2
 *					Enseignant :
 *						J01 : Mehdi Lahlou
 *                      J02 : Gilbert Babin
 */

 use AdventureWorks2019
 go

/*
	Question #1 :
		AdventureWorks aimerait mettre en œuvre le modèle de vitalité ("The vitality model") de l'ancien président-directeur général de General Electric,
		Jack Welch, qui a été décrit comme un système "20-70-10". Les "20% les plus importants" des employés sont les plus productifs et 70% (les "70 
		indispensables") travaillent correctement. Les 10% restants sont des non-producteurs et doivent être licenciés.

		En utilisant une clause de classement et une sous-requête, vous devez écrire une requête pour identifier les "20% les plus performants" des vendeurs
		(pour les féliciter et les encourager!) ainsi que les 10% les moins performants  (pour les mettre à la porte <insèrez le rire diabolique ici>) !!
		On ne veut donc pas voir apparaître dans le rapport les vendeurs appartenant au 70% restant.

		(Par vendeurs, on fait référence aux commis de vente, peu importe le titre de poste.)
		
		Comme AdventureWorks vend essentiellement des vélos, le printemps (mars à mai, incl.) est crucial pour ses résultats financiers.
		Donc, l'analyse doit uniquement tenir compte du sous-total des ventes que les vendeurs ont réalisées pour 
		cette période, quelle que soit l'année. Il faut afficher, pour chaque résultat :

			- L'identifiant du vendeur
			- Le "National ID Number" du vendeur
			- Le prénom du vendeur
			- Le nom de famille du vendeur
			- La somme du sous-total vendu par le vendeur pour le quatrième trimestre (formaté en dollars, c.-à-d. $xxx.xx)
			- Le rang en pourcentage du vendeur (formaté en pourcentage avec deux points de précision)
			- Le décile auquel appartient le vendeur
			- Un message personnalisé pour : le 1er décile 'Excellente performance !'; le 2e décile 'Continuez, vous allez bien !'
				le 10e décile 'Cherchez vous un emploi ailleurs !'
*/



select 
       *,
	    Case
			when Decile =1 then 'Excellente performance !'
			when Decile =2 then 'Continuez, vous allez bien !'
			when Decile =10 then 'Cherchez vous un emploi ailleurs !'
			else 'Whatever'
		end as 'Status'
 from (
    select
       soh.SalesPersonID,
		e.NationalIDNumber,
		p.FirstName,
		p.LastName,
		FORMAT(SUM(soh.SubTotal), 'c', 'en-us') as 'Somme sous-total Ventes',
		/*Le rang en pourcentage du vendeur (formaté en pourcentage avec deux points de précision)*/
		FORMAT(ROUND(percent_rank() over(order by sum(soh.SubTotal) desc), 2), 'p') as 'Le rang en %',
		NTILE(10) over(order by SUM(soh.SubTotal) desc) as 'Decile'
		from
	Sales.SalesOrderHeader soh
	inner join Sales.SalesPerson sp1 on soh.SalesPersonID = sp1.BusinessEntityID
	inner join Person.Person p on sp1.BusinessEntityID = p.BusinessEntityID
	inner join HumanResources.Employee e on p.BusinessEntityID= e.BusinessEntityID

	where Month(OrderDate) in (3,4,5) 
	group by soh.SalesPersonID, e.NationalIDNumber, p.FirstName, p.LastName
	) as Table2
	where Decile in (1,2,10)
	


/*
	Question #2 :
		AdventureWorks voudrait explorer les achats d'accessoires (produits non-fabriqués) effectués par ses clients. On s'interesse particulièrement aux accessoires qui ont
		été commandés par des magasins situés au Canada en même temps qu'ils ont effectués des achats vélos (produits fabriqués par AdventureWorks).
		Donc, les données doivent être affichées seulement pour les ventes faites aux magasins (pas de clients individuels) qui ont achetés des vélos.
		
		En utilisant une CTE, vous devez afficher une liste contenant les informations groupées par l'identificateur du produit, le nom du produit,
		le numéro du produit.

		Votre rapport doit contenir seulement quatre colonnes comme suit:
		
		ProductID	|Name						|ProductNumber	|OrderCount	|Rang
		715			|Long-Sleeve Logo Jersey, L	|LJ-0192-L		|238		|1
		712			|AWC Logo Cap				|CA-1098		|237		|2
		708			|Sport-100 Helmet, Black	|HL-U509		|190		|3
		...			|...						|...			|...		|...

		Ceci indique par exemple que, pour l’ensemble des commandes faites par des magasins dans lesquelles des produits fabriqués ont été achetés, 238 
		commandes incluaient également l’achat du produit 715 (Long-Sleeve Logo Jersey, L), 237 commandes incluaient l’achat du produit 712	(AWC Logo Cap),
		etc. Le rang utilisé ne permet pas de sauts de valeur.

		Trier par "OrderCount", par ordre décroissant.
*/

	--7385 
with CTEQ2(ProductID, Name, ProductNumber,SalesOrderID, SalesOrderDetailID) as
(
select  
pt.ProductID, pt.Name, pt.ProductNumber,
soh.SalesOrderID, sod.SalesOrderDetailID
		
	from Sales.SalesOrderHeader soh 
	inner join Sales.Customer c on c.CustomerID = soh.CustomerID
	inner join Person.BusinessEntityAddress bea on c.StoreID = bea.BusinessEntityID
	inner join person.Address a on a.AddressID = bea.AddressID
	inner join Sales.SalesOrderDetail sod on soh.SalesOrderID = sod.SalesOrderID
	inner join Production.Product pt on sod.ProductID = pt.ProductID
	inner join Person.StateProvince sp on sp.StateProvinceID =a.StateProvinceID
	where sp.CountryRegionCode = 'CA' AND  pt.MakeFlag =1 --AND soh.SalesOrderID='55280'
	)
	select
	p.ProductID,
	p.Name,
	p.ProductNumber,
	COUNT( distinct(CTEQ2.SalesOrderID)) as OrderCount,
	dense_rank()over(order by COUNT(distinct(CTEQ2.SalesOrderID)) desc) as 'Rang'
	from CTEQ2
	inner join Sales.SalesOrderDetail sod on sod.SalesOrderID = CTEQ2.SalesOrderID and sod.SalesOrderDetailID <> CTEQ2.SalesOrderDetailID 
	inner join Production.Product p on p.ProductID = sod.ProductID
	where p.MakeFlag =0
	group by  p.ProductID, p.[Name], p.ProductNumber;




/*
	Question #3 a) :
		On vous demande de fournir une requête affichant les détails suivants sur les fournisseurs actifs, avec statut privilégié, chez
		lesquels AdventureWorks a fait moins de 30 commandes. Afficher :
			- L'identifiant du fournisseur
			- La date de la commande
			- Un numéro de séquence attribué à chaque commande faite auprés du fournisseur, en débutant avec la plus récente commande
			- Le sous-total de chaque commande (formaté en dollars, c.-à-d. $xxx.xx)
*/



select poh.VendorID
    
	, poh.OrderDate, row_number()over(partition by poh.VendorID order by OrderDate desc) as 'No_sequence' , FORMAT(poh.SubTotal, 'C')
	from Purchasing.PurchaseOrderHeader poh
	inner join Purchasing.Vendor v on v.BusinessEntityID = poh.VendorID
	where ActiveFlag =1 AND PreferredVendorStatus=1 AND 
    poh.VendorID in (select VendorID  from Purchasing.PurchaseOrderHeader group by VendorID having count(PurchaseOrderID)<=30);
	




/*
	Question #3 b) :
		AdventureWorks voudrait savoir qui parmi ces fournisseurs privilégiés (chez lesquels AdventureWorks a fait moins de 30 commandes) a
		tendance à augmenter ses prix. L'entreprise souhaite utiliser ces informations afin de leur retirer le statut de "fournisseur
		privilégié". On émet ici l'hypothèse que les commandes auprès d'un fournisseur restent stables à travers le temps et sont donc
		toujours pour des produits/quantités similaires.
				
		En utilisant une CTE basée sur la requête de la Partie a), construisez une requête qui affichera la liste des fournisseurs pour lesquels
		le montant moyen (en utilisant le sous-total) de leurs trois commandes les plus récentes est supérieur au montant moyen qu’ils ont demandé 
		à AdventureWorks jusqu’à présent.

		On voudra afficher :
			- L'identifiant du fournisseur
			- Le montant moyen des toutes les commandes faites auprès du fournisseur
			- Le montant moyen des trois commandes les plus récentes faites auprès du fournisseur
			- La différence entre le montant moyen des trois commandes les plus récentes faites auprès du fournisseur et le montant moyen des
				toutes les commandes faites auprès du fournisseur.

		Votre rapport doit contenir seulement ces quatre colonnes et être filtré par la diminution relative aux coûts d'acquisition, de façon 
		que la réduction la plus importante soit en tête de la liste. Tous les montants doivent être formatés en dollars, c.-à-d. $xxx.xx.
*/




with CTEQ3b(VendorID, OrderDate, RowNum, SubTotal) as
(select poh.VendorID    
	, poh.OrderDate, 
	row_number()over(partition by poh.VendorID order by OrderDate desc) , 
	poh.SubTotal
	from Purchasing.PurchaseOrderHeader poh
	inner join Purchasing.Vendor v on v.BusinessEntityID = poh.VendorID
	where v.ActiveFlag =1 AND v.PreferredVendorStatus=1 AND 
    poh.VendorID in (select VendorID  from Purchasing.PurchaseOrderHeader group by VendorID having count(PurchaseOrderID)<=30)
	
)
select 
c.VendorID,

(select format(avg(Subtotal),'C') from Purchasing.PurchaseOrderHeader poh where c.VendorID=poh.VendorID) as 'total',
format(avg(c.SubTotal),'C') as 'total 3' ,
Format(avg(SubTotal) - (select avg(Subtotal) from Purchasing.PurchaseOrderHeader poh where c.VendorID=poh.VendorID),'C') as 'difference'
from CTEQ3b c
where c.RowNum<=3
group by c.VendorID
having avg(SubTotal) - (select avg(Subtotal) from Purchasing.PurchaseOrderHeader poh where c.VendorID=poh.VendorID) >0
order by avg(SubTotal) - (select avg(Subtotal) from Purchasing.PurchaseOrderHeader poh where c.VendorID=poh.VendorID) desc

```