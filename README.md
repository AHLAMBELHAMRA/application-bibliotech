#include <stdio.h>
#include <stdlib.h>


/* run this program using the console pauser or add your own getch, system("pause") or input loop */
typedef struct livre {
	char titre[30];
	char auteur[30];
	float prix;
	int stock;
	struct livre *suivant;
} livre;
typedef struct utilisateur {
	char nom[20];
	long int num;
	char pswd[20];
	struct utilisateur *suivant;
} utilisateur;
typedef struct admin {
	char nom[20];
	char pswd[20];
	int num_fonc;
	struct admin *suivant;
} admin;

//file des commandes
typedef struct commande {
	livre *L;
	utilisateur *U;
	int quantite;
	float pt;
	struct commande *suiv;
} commande;
typedef struct file {
	commande *tete;
	commande *queu;
} file;
//affichage du premier commande
void afich_cmd(file *F) {
	commande *cmd =F->tete;
	if(F->tete==NULL) {
		printf("\nla file des commandes est vide !");
	} else {
		printf("\ntitre: %s \nla quantite : %d\nle prix totale : %.2f\nnom du client : %s \n num : %ld\n ",cmd->L->titre,cmd->quantite,cmd->pt,cmd->U->nom,cmd->U->num);
	}
}
//fonction d'ajout utilisateur
utilisateur* ajout_utilisateur (utilisateur *U,char nom[20],long int num,char pswd[20]) {
	utilisateur *nouveau=(utilisateur*)malloc(sizeof(utilisateur));
	if(nouveau==NULL) {
		printf("\n erreurs d'allocation dynamique.");
		exit(0);
	}
	strcpy(nouveau->nom,nom);
	nouveau->num=num;
	strcpy(nouveau->pswd,pswd);
	nouveau->suivant=U;
	return nouveau;
}
//fonction de connexion
utilisateur* connect(utilisateur *U,char n[20],char pswd[20]) {
	while(U!=NULL) {
		if(strcmp(n,U->nom)==0) {
			if(strcmp(pswd,U->pswd)==0)
				return U;
		}
		U=U->suivant;
	}
	return NULL;
}
//fonction d'affichage des info d'un utilisateur
void inf(utilisateur *p) {
	printf("\n_____________________________________________\n");
	printf("\n NOM: %s",p->nom);
	printf("\n tele: %ld",p->num);

}
//fonction de modification des info personnelles d'administrateur
void inf_admin(admin *adm) {
	int choix,num;
	char nom[20],pswd[20];
INFO:
	printf("\n__________________________________________________\n");
	printf("\nNOM : %s",adm->nom);
	printf("\nNUMERO : %d",adm->num_fonc);
	printf("\n1-modifier le nom \n2-modifier le numero d'admin \n3-changer le mot de passe\n");
	scanf("%d",&choix);
	switch(choix) {
		case 1:
			printf("\nentrer le nouveau nom : ");
			scanf("%s",nom);
			strcpy(adm->nom,nom);
			printf("votre nom est bien modifier !");
			goto INFO;
		case 2:
			printf("\nentrer le nouveau numero : ");
			scanf("%d",num);
			adm->num_fonc=num;
			printf("votre numero est bien modifier !");
			goto INFO;
		case 3 :
			do {
				printf("\nle mot de passe actuel :");
				scanf("%s",pswd);
			} while(strcmp(adm->pswd,pswd)!=0);
			printf("\nle nouveau mot de passe :");
			scanf("%s",pswd);
			strcpy(adm->pswd,pswd);
			printf("\nvotre mot de passe est bien changer!");
			goto INFO;
		default:
			break;

	}

}
//fonction de modification des info personnelles utilisateur
void inf_per(utilisateur *utl) {
	int choix;
	long int num;
	char nom[20],mpass[20];
inf:
	inf(utl);
	printf("\n1-modifier le nom \n2-modifier le numero du tele\n3-changer le mot de passe\n");
	scanf("%d",&choix);
	switch(choix) {
		case 1:
			printf("\nentrer le nouveau nom :");
			scanf("%s",nom);
			strcpy(utl->nom,nom);
			printf("\nvotre nom d'utilisateur est bien modifier !");
			goto inf;
		case 2:
			printf("\nentrer le nouveau num :");
			scanf("%ld",&utl->num);
			printf("\nvotre numero de telephone est bien modifier !");
			goto inf;
		case 3:
			do {
				printf("\nle mot de passe actuel :");
				scanf("%s",mpass);
			} while(strcmp(utl->pswd,mpass)!=0);
			printf("\nle nouveau mot de passe :");
			scanf("%s",mpass);
			strcpy(utl->pswd,mpass);
			printf("\nvotre mot de passe est bien changer!");
			goto inf;
		default :
			break;

	}
}
//connexion fonctionnre
admin* connect_adm(char n[20], char pswd[20],admin *LF ) {
	while(LF!=NULL) {
		if(strcmp(n,LF->nom)==0) {
			if(strcmp(pswd,LF->pswd)==0)
				return LF;
		}
		LF=LF->suivant;
	}
	return NULL;
}

//ajout administrateur
admin* ajout_admin(admin *LA,int num,char n[20],char pswd[20]) {
	admin *nouveau=(admin*)malloc(sizeof(admin));
	if(nouveau==NULL) {
		printf("\n erreurs d'allocation dynamique.");
		exit(0);
	}
	strcpy(nouveau->nom,n);
	strcpy(nouveau->pswd,pswd);
	nouveau->num_fonc=num;
	nouveau->suivant=LA;
	return nouveau;
}

//passer un commande
void ajout_cmd(int N,utilisateur *u,livre *LV,file *F) {
	int i,q;
	commande *nouveau=(commande*)malloc(sizeof(commande));
	for(i=1; i<N; i++)//boucle pour acceder au livre demander
		LV=LV->suivant;
	printf("la quantite :");
	scanf("%d",&q);
	if(q > LV->stock ||q<=0) {
		printf("\n cette quantite n'est pas disponible maintenant .");
	} else {
		//ajout a le file de commande
		nouveau->L=LV;
		nouveau->U=u;
		nouveau->quantite=q;
		nouveau->pt=(LV->prix)*q;
		if( (F->tete) == NULL) {//si la file est vide
			F->tete=nouveau;
			F->queu=nouveau;
		} else {
			F->queu->suiv=nouveau;
			F->queu=nouveau;
		}
		printf("\n votre commande de montant :%.2f dh est bien passer ,nous allons appler pour confermer la commande.",nouveau->pt)	;
	}

}
//affichage des livres
void afich_livre(livre *LV,file *F) {
	int i=1,N;
	printf("\n    titre         |     auteur            |  prix     | stock ");
	while(LV!=NULL) {
		printf("\n%d- %s  | %s | %.2f | %d \n",i,LV->titre,LV->auteur,LV->prix,LV->stock);
		LV=LV->suivant;
		i++;
	}
}
//chercher un livre
livre* cher_livre(livre *LV,char titre[]) {
	while(LV!=NULL) {
		if(strcmp(LV->titre,titre)==0) {
			return LV;
		}
		LV=LV->suivant;
	}
	return NULL;
}
//ajouter un livre
livre* ajout_livre(char t[],char au[],float px,int s,livre *L) {
	livre *new_livre=(livre*)malloc(sizeof(livre));
	strcpy((new_livre->titre),t);
	strcpy((new_livre->auteur),au);
	new_livre->prix=px;
	new_livre->stock=s;
	new_livre->suivant=L;
	return new_livre;
}
//afichage de liste utilisateurs
void liste_utl(utilisateur *utl) {
	while(utl!=NULL) {
		printf("\nNOM : %s           tele : %ld",utl->nom,utl->num);
		utl=utl->suivant;
	}
}
//suprimer la tete de la file
file* supp_file(file *F) {
	commande *temp;
	temp=F->tete;
	F->tete=F->tete->suiv;
	free(temp);
	return F;
}
//supresion du compte utilsateur
utilisateur* supp_utl(utilisateur *utl ,utilisateur *head) { //hear est l'adresse de la liste(premier element)
	utilisateur *actuel ,*precedent;
	if(utl==head) { //le compte a supp est le premier element de la liste
		actuel=head;
		head=head->suivant;
		free(actuel);
		return head;
	}
	precedent= head;
	actuel =head->suivant;
	while(actuel!=NULL && actuel !=utl) {
		precedent=actuel;
		actuel= actuel->suivant;
	}
	if(actuel==NULL)
		return NULL;
	precedent->suivant=actuel->suivant;
	free(actuel);
	return head;
}
//supprimer un administrateur
admin* supp_admin(admin *adm,admin *head) {
	admin *actuel,*precedent;
	if(adm==head) {
		actuel=head;
		head=head->suivant;
		free(actuel);
		return head;
	}
	precedent=head;
	actuel=head->suivant;
	while(actuel!=NULL &&actuel !=adm) {
		precedent=actuel;
		actuel=actuel->suivant;
	}
	if(actuel==NULL)
		return NULL;
	precedent->suivant=actuel->suivant;
	free(actuel);
	return head;
}
//recherche admin par nom
admin* rech_admin(char n[20],admin *LA) {
	while(LA!=NULL) {
		if(strcmp(n,LA->nom)==0)
			return LA;
		LA=LA->suivant;
	}
	return NULL;
}
//fonction de recherche utilisateur par nom
utilisateur* rech_user (char n[20],utilisateur *U) {
	while(U!=NULL) {
		if(strcmp(n,U->nom)==0)
			return U;
		U=U->suivant;
	}
	return NULL;
}
//afichage liste admin
void afich_admin(admin *LA) {
	while(LA!=NULL) {
		printf("\nnom : %s     numero : %d",LA->nom,LA->num_fonc);
		LA=LA->suivant;
	}
}
//operations sur les livres
void op_livre(livre *L) {
	int choix, s;
	char t[30], A[30];
	float p;
	livre *l;

op_lv:
	do {
		printf("\n1- Ajouter un livre\n2- Modifier un livre\n");
		scanf("%d", &choix);
	} while (choix < 1 || choix > 2);
	getchar();

	if (choix == 1) {
		printf("\nTitre : ");
		fgets(t, 30, stdin);
		t[strcspn(t, "\n")] = 0;
		printf("Auteur : ");
		fgets(A, 30, stdin);
		A[strcspn(A, "\n")] = 0;
		printf("Prix : ");
		scanf("%f", &p);
		printf("Stock : ");
		scanf("%d", &s);
		L = ajout_livre(t, A, p, s, L);
		printf("Le livre a été ajouté avec succès à la liste.\n");
		goto op_lv;
	} else {
		printf("\nTitre de la livres a modifier  : ");
		fgets(t, 30, stdin);
		t[strcspn(t, "\n")] = 0;
		l = cher_livre(L, t);
		if (l != NULL) {
			printf("Titre : %s   Auteur : %s   Prix : %.2f   Stock : %d\n", l->titre, l->auteur, l->prix, l->stock);
			printf("Entrez les nouvelles informations successivement : \n");
			printf("Titre : ");
			fgets(t, 30, stdin);
			t[strcspn(t, "\n")] = 0;
			printf("Auteur : ");
			fgets(A, 30, stdin);
			A[strcspn(A, "\n")] = 0;
			printf("Prix : ");
			scanf("%f", &p);
			printf("Stock : ");
			scanf("%d", &s);
			strcpy(l->titre, t);
			strcpy(l->auteur, A);
			l->prix = p;
			l->stock = s;
			printf("Les informations ont été modifiées avec succès.\n");
			goto op_lv;
		} else {
			printf("Le livre n'a pas été trouvé.\n");
		}
	}
}


int main(int argc, char *argv[]) {
	int C,nmr,N;
	long int num;
	char n[20],pswd[30],titre[30];
	admin *LA=NULL,*adm=NULL;
	file *F=(file*)malloc(sizeof(file));
	livre *programmation=NULL,*securite=NULL,*L;
	utilisateur *U=NULL,*utl=NULL;
	F->queu=NULL;
	F->tete=NULL;
	programmation=ajout_livre("programmer en C","claude delannoy",149.49,10,programmation);
	U=ajout_utilisateur(U,"hayat_janah",060000,"123abcd4");
	U=ajout_utilisateur(U,"ahlam_belhamra",06111111,"12345678");
	LA=ajout_admin(LA,111111,"admin_generale","12345678");



	printf("bienvenue sur votre bibliotech enligne !");
	printf("\n___________________________________________\n");
debit:
	do {
		printf("\nutiliser la bibliotheque :\n1-utilisateur \n2-administrateur \n");
		scanf("%d",&C);
	} while(C<1||C>2);
	if(C==1) { //utilisateur
		do {
			printf("\n1-se connecter\n2-s'inscrire\n");
			scanf("%d",&C);
		} while(C<1||C>2);
		if(C==1) { //se connecter
conexion:
			printf("\nNOM: ");
			scanf("%s",n);
			printf("\nMot de passe: ");
			scanf("%s",pswd);
			utl=connect(U,n,pswd);
			if(utl==NULL) {
				printf("\n le nom ou le mot de passe est incorrect..");
				goto conexion;
			} else { //contenu
				printf("\n*************************************************************************\n");
				do {
					printf("\n1-menu    2-parametres\n");
					scanf("%d",&C);
				} while((C<1)||(C>2));
				if(C==1) { //menu
menu1:
					do {
						printf("\n1-livres de programmation\n2-livre de securite \n");
						scanf("%d",&C);
					} while((C<1)||(C>2));
					if(C==1) { //programmation
						do {
							printf("\n1-afficher la liste des livres disponible\n2-chercher un livre.\n");
							scanf("%d",&C);
						} while((C<1)||(C>2));
						if(C==1) { //afichager et comande
							afich_livre(programmation,F);
							printf("\n\n\n choisi un livre\n");
							scanf("%d",&N);
							ajout_cmd(N,utl,programmation,F);
							goto menu1;
						} else { //recherch d'un livre
							getchar();
							printf("\ntitre : ");
							fgets(titre,30, stdin);
							titre[strcspn(titre, "\n")] = 0;
							L=cher_livre(programmation,titre);
							if(L==NULL) {
								printf("\n aucune resultats,");
								goto menu1;
							} else {
								printf("\ntitre   |  auteur  |  prix  | stock  ");
								printf("\n%s | %s | %f | %d ",L->titre,L->auteur,L->prix,L->stock);
								printf("\n\n1-commande \n2-retour\n");
								scanf("%d",&C);
								if(C==1) {
									ajout_cmd(0,utl,L,F);
								} else
									goto menu1;

							}
						}

					} else { //securite
						do {
							printf("\n1-afficher la liste des livres disponible\n2-chercher un livre.\n");
							scanf("%d",&C);
						} while((C<1)||(C>2));
						if(C==1) { //afichager et comande
							afich_livre(securite,F);
						} else { //recherch d'un livre
							getchar();
							printf("\ntitre : ");
							fgets(titre,30, stdin);
							titre[strcspn(titre, "\n")] = 0; // supprime le caractère de nouvelle ligne de fgets()

							L=cher_livre(securite,titre);
							if(L==NULL) {
								printf("\n aucune resultats,");
								goto menu1;
							} else {
								printf("\ntitre   |  auteur  |  prix  | stock  ");
								printf("\n%s | %s | %f | %d ",L->titre,L->auteur,L->prix,L->stock);
								printf("\n\n1-commande \n2-retour\n");
								scanf("%d",&C);
								if(C==1) {
									ajout_cmd(0,utl,L,F);
								} else
									goto menu1;

							}
						}
					}
				} else { //parametres
parametres1:
					printf("\n1-informations personnelles\n2-supprimer le compte\n");
					scanf("%d",&C);
					switch(C) {
						case 1://informations personnelles
							inf_per(utl);
							break;
						case 2://suppression du compte
							if(supp_utl(utl,U)!=NULL) {
								U=supp_utl(utl,U);
								printf("\nvotre compte est supprime .");
								goto debit;

							} else {
								printf("\nprobleme de suppression resayez une autre fois ");
								goto parametres1;
							}
						default :
							goto parametres1;
					}
				}
			}


		} else { //creation d'un nouveau compte
			printf("\nNOM (sous forme prenom_nom): ");
			scanf("%s",&n);
			printf("\nnumero du tele: ");
			scanf("%ld",&num);
			printf("\nMot de passe : ");
			scanf("%s",pswd);
			U=ajout_utilisateur(U,n,num,pswd);
			printf("\nvotre compte est bien creer.");
			printf("\n*************************************************************************\n");
			do {
				printf("\n1-menu    2-parametres\n");
				scanf("%d",&C);
			} while((C<1)||(C>2));
			if(C==1) { //menu
menu2:
				do {
					printf("\n1-livres de programmation\n2-livre de securite \n");
					scanf("%d",&C);
				} while((C<1)||(C>2));
				if(C==1) { //programmation
					do {
						printf("\n1-afficher la liste des livres disponible\n2-chercher un livre.\n");
						scanf("%d",&C);
					} while((C<1)||(C>2));
					if(C==1) { //afichager et comande
						afich_livre(programmation,F);
						printf("\n\n\n choisi un livre\n");
						scanf("%d",&N);
						ajout_cmd(N,utl,programmation,F);
						goto menu2;
					} else { //recherch d'un livre
						getchar();
						printf("\ntitre : ");
						fgets(titre,30, stdin);
						titre[strcspn(titre, "\n")] = 0;
						L=cher_livre(programmation,titre);
						if(L==NULL) {
							printf("\n aucune resultats,");
							goto menu2;
						} else {
							printf("\ntitre   |  auteur  |  prix  | stock  ");
							printf("\n%s | %s | %f | %d ",L->titre,L->auteur,L->prix,L->stock);
							printf("\n\n1-commande \n2-retour\n");
							scanf("%d",&C);
							if(C==1) {
								ajout_cmd(0,utl,L,F);
							} else
								goto menu2;

						}
					}

				} else { //securite
					do {
						printf("\n1-afficher la liste des livres disponible\n2-chercher un livre.\n");
						scanf("%d",&C);
					} while((C<1)||(C>2));
					if(C==1) { //afichager et comande
						afich_livre(securite,F);
					} else { //recherch d'un livre
						getchar();
						printf("\ntitre : ");
						fgets(titre,30, stdin);
						titre[strcspn(titre, "\n")] = 0; // supprime le caractère de nouvelle ligne de fgets()

						L=cher_livre(securite,titre);
						if(L==NULL) {
							printf("\n aucune resultats,");
							goto menu2;
						} else {
							printf("\ntitre   |  auteur  |  prix  | stock  ");
							printf("\n%s | %s | %f | %d ",L->titre,L->auteur,L->prix,L->stock);
							printf("\n\n1-commande \n2-retour\n");
							scanf("%d",&C);
							if(C==1) {
								ajout_cmd(0,utl,L,F);
							} else
								goto menu2;

						}
					}
				}
			} else { //parametres
parametres2:
				printf("\n1-informations personnelles\n2-supprimer le compte\n");
				scanf("%d",&C);
				utl=U;
				switch(C) {
					case 1://informations personnelles
						inf_per(utl);
						break;
					case 2://suppression du compte
						if(supp_utl(utl,U)!=NULL) {
							U=supp_utl(utl,U);
							printf("\nvotre compte est supprime .");
							goto debit;

						} else {
							printf("\nprobleme de suppression resayez une autre fois ");
							goto parametres2;
						}

					default :
						goto parametres2;
				}
			}
		}
	} else { //administarateur
ADMIN:
		do {
			printf("1- se connecter\n2-creer un compte\n");
			scanf("%d",&C);
		} while((C<1)||(C>2));
		if(C==1) { //connexion
			printf("\nNOM : ");
			scanf("%s",n);
			printf("\nmot de passe : ");
			scanf("%s",pswd);
			adm=connect_adm(n,pswd,LA);
			if(adm==NULL) {
				printf("\nle no ou le mot de passe est incorrect ! \n");
				goto ADMIN;
			} else { //compte existe
MENU:
				do {
					printf("\n1-menu\n2-parametres\n");
					scanf("%d",&C);
				} while((C<1)||(C>2));
				if(C==1) { //menu
					printf("\n1-les commandes \n2-les utilisateurs \n3-les livres \n4-les administarteurs\n");
					scanf("%d",&C);
					switch(C) {
						case 1://les commandes
							afich_cmd(F);
							printf("\n1-envoyer la commande \n2-retour ");
							scanf("%d",&C);
							if(C==1) {
								F=supp_file(F);
								printf("\ncette commande est supprimer de la file des commande");
								goto MENU;

							} else {
								goto MENU;
							}
						case 2://les utilisateurs
							do {
								printf("\n1-voire la liste des utilisateurs \n2-supprimer utilisateur\n ");
								scanf("%d",&C);
							} while((C<1)||(C>2));
							if(C==1) { //afichage des listes des utilisateurs
								liste_utl(U);
								goto MENU;
							} else { //supprimer utilisateur
								printf("\ndonner le nom de l'utilisateur :");
								scanf("%s",n);
								utl=rech_user(n,U);
								if(utl==NULL) {
									printf("\nce nom d'utilisateur n'existe pas !.");
									goto MENU;
								}
								if(supp_utl(utl,U)!=NULL) {
									U=(supp_utl(utl,U));
									printf("\n ce compte est bien supprimer .");
									goto MENU;
								}
							}
						case 3://les livres
							do {
								printf("\n1-liste de programmation \n2-liste de securite \n");
								scanf("%d",&C);
							} while((C<1)||(C>2));
							if(C==1) { //pro
								op_livre(programmation);
								goto MENU;
							} else {
								op_livre(securite);
								goto MENU;
							}
						case 4://les administrateur
							if(adm->num_fonc==111111) {
								//affichage de liste admin
								afich_admin(LA);
								do {
									printf("\n1-ajouter un admin \n2-supprimer un admin");
									scanf("%d",&C);
								} while((C<1)||(C>2));
								if(C==1) { //ajout
									printf("NOM: ");
									scanf("%s",n);
									printf("\n numero :");
									scanf("%d",nmr);
									printf("\n mot de passe :");
									scanf("%s",pswd);
									LA=ajout_admin(LA,nmr,n,pswd);
									printf("\n cette operation est bien passer !.");
									goto MENU;
								} else { //supresion
									printf("nom d'administrateur a supprimer : ");
									scanf("%s",n);
									if(rech_admin(n,LA)==NULL) {
										printf("\nce nom d'admin n'existe pas !");
										goto MENU;
									} else {
										adm=rech_admin(n,LA);
										printf("\n1-supprimer %s ,\n2-annuler",n);
										scanf("%d",&C);
										if(C==1) {
											LA=supp_admin(adm,LA);
											printf("le compte est bien supprimer .");
											goto MENU;
										}
										goto MENU;
									}
								}
							} else {
								printf("vous n'avait pas le droit d'acces a cette option");
								break;
							}

					}


				} else { //parametres
					printf("\n1-informations personnelles\n2-supprimer le compte\n");
					scanf("%d",&C);
					switch(C) {
						case 1://informations personnelles
							inf_admin(adm);
							break;
						case 2://suppression du compte
							printf("\n1-supprimer\n2-annuler\n");
							scanf("%d",&C);
							if(C==1) {
								LA=supp_admin(adm,LA);
								printf("\nvotre compte est supprime ..");
								goto debit;
							}
							goto ADMIN;

						default :
							break;
					}
				}
			}

		} else { //creation d'un nouveau compte
			printf("\nNOM: ");
			scanf("%s",n);
			printf("\nNUMERO D'ADMIN : ");
			scanf("%d",&nmr);
			printf("\nMOT DE PASSE : ");
			scanf("%s",pswd);
			if(nmr!=786841) {
				printf("\nvous n'avait pas le droit de creer un compte administrateur .\n");
				goto debit;
			} else {
				LA=ajout_admin(LA,nmr,n,pswd);
				adm=LA;
				printf("\n______________________________________\n");
MENU1 :
				do {
					printf("\n1-menu\n2-parametres\n");
					scanf("%d",&C);
				} while((C<1)||(C>2));
				if(C==1) { //menu
					printf("\n1-les commandes \n2-les utilisateurs \n3-les livres \n4-les administarteurs\n");
					scanf("%d",&C);
					switch(C) {
						case 1://les commandes
							afich_cmd(F);
							printf("\n1-envoyer la commande \n2-retour ");
							scanf("%d",&C);
							if(C==1) {
								F=supp_file(F);
								printf("\ncette commande est supprimer de la file des commande");
								goto MENU1;

							} else {
								goto MENU1;
							}
						case 2://les utilisateurs
							do {
								printf("\n1-voire la liste des utilisateurs \n2-supprimer utilisateur\n ");
								scanf("%d",&C);
							} while((C<1)||(C>2));
							if(C==1) { //afichage des listes des utilisateurs
								liste_utl(U);
								goto MENU1;
							} else { //supprimer utilisateur
								printf("\ndonner le nom de l'utilisateur :");
								scanf("%s",n);
								utl=rech_user(n,U);
								if(utl==NULL) {
									printf("\nce nom d'utilisateur n'existe pas !.");
									goto MENU1;
								}
								if(supp_utl(utl,U)!=NULL) {
									U=(supp_utl(utl,U));
									printf("\n ce compte est bien supprimer .");
									goto MENU1;
								}
							}
						case 3://les livres
							do {
								printf("\n1-liste de programmation \n2-liste de securite \n");
								scanf("%d",&C);
							} while((C<1)||(C>2));
							if(C==1) { //pro
								op_livre(programmation);
								goto MENU1;
							} else {
								op_livre(securite);
								goto MENU1;
							}
						case 4://les administrateur
							if(adm->num_fonc==111111) {
								//affichage de liste admin
								afich_admin(LA);
								do {
									printf("\n1-ajouter un admin \n2-supprimer un admin");
									scanf("%d",&C);
								} while((C<1)||(C>2));
								if(C==1) { //ajout
									printf("NOM: ");
									scanf("%s",n);
									printf("\n numero :");
									scanf("%d",nmr);
									printf("\n mot de passe :");
									scanf("%s",pswd);
									LA=ajout_admin(LA,nmr,n,pswd);
									printf("\n cette operation est bien passer !.");
									goto MENU1;
								} else { //supresion
									printf("nom d'administrateur a supprimer : ");
									scanf("%s",n);
									if(rech_admin(n,LA)==NULL) {
										printf("\nce nom d'admin n'existe pas !");
										goto MENU1;
									} else {
										adm=rech_admin(n,LA);
										printf("\n1-supprimer %s ,\n2-annuler",n);
										scanf("%d",&C);
										if(C==1) {
											LA=supp_admin(adm,LA);
											printf("le compte est bien supprimer .");
											goto MENU1;
										}
										goto MENU1;
									}
								}
							} else {
								printf("vous n'avait pas le droit d'acces a cette option");
								break;
							}
					}
				} else { //parametres
					printf("\n1-informations personnelles\n2-supprimer le compte\n");
					scanf("%d",&C);
					switch(C) {
						case 1://informations personnelles
							inf_admin(adm);
							break;
						case 2://suppression du compte
							printf("\n1-supprimer\n2-annuler\n");
							scanf("%d",&C);
							if(C==1) {
								LA=supp_admin(adm,LA);
								printf("\nvotre compte est supprime ..");
								goto debit;
							}
							goto ADMIN;

						default :
							break;
					}
				}
			}
		}

	}
	return 0;
}
