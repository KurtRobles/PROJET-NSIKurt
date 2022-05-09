# PROJET-NSIKurt
    **PUISSANCE 4** /
Jeu de stratégie combinatoire abstrait, pour gagner une partie de puissance 4, il suffit d'être le premier à aligner 4 jetons de sa couleur horizontalement,
verticalement et diagonalement.
Si lors d'une partie, tous les jetons sont joués sans qu'il y est d'alignement de jetons, la partie est déclaré nulle.

  1) Faire des recherches sur internet pour trouer de quoi faire des cases pour le jeu
  2) Tester chaque recherche faire des tests pour voir lesquelles je vais utiliser 
  3) Faire en sorte qu'l n'y ait pas colision à chaque qu'on place un jeton 
 

import os

ROUGE = '\033[31m'
BLEU = '\033[36m'
JAUNE = '\033[33m'
BLANC = '\033[0m'

# defini la couleur 
def colorier(couleur, str):
    return f'{couleur}{str}{BLANC}'

# defini le numero de chaque grille 
def afficher_grille():
    print('1 2 3 4 5 6 7')
    for ligne in reversed(grille_jeu):
        print(' '.join(ligne))

# demande au joueur de choisir une colonne entre 1 et 7
def demander_colonne(n_joueur, jeton):
    colonne = input(
        f'\nÀ "{nom_joueurs[n_joueur]}" de placer {jeton} (1 à 7): ')
    if colonne.isnumeric() == False:
        raise Exception('Veuillez choisir un nombre de 1 à 7')

    colonne_int = int(colonne)
    if (colonne_int < 1) or (colonne_int > 7):
        raise Exception('Veuillez choisir de 1 à 7')

    return colonne_int - 1

# defini la personne qui doit joué et si c'est a son toure 
def placer_jeton(colonne, jeton):
    for ligne in range(6):
        if grille_jeu[ligne][colonne] == '.':
            grille_jeu[ligne][colonne] = jeton
            return [ligne, colonne]
    # si le joueur depasse la limite de jeton dans une colonne alors on lui envoie ca 
    raise Exception('Veuillez choisir une colonne non-remplie')

# defini si il y a plus de possibilité de gagné pour les deux joueurs et donc stop le programme 
def verif_egalite():
    return ('.' in sum(grille_jeu, [])) == False

# demande aux joueurs d'appuyez sur entrée pour relancer une partie en cas d'egalité
def fin_jeu_egalite():
    global grille_jeu
    afficher_grille()
    grille_jeu = [['.'] * 7 for _ in range(6)]
    print()
    input('Appuyez sur entrée pour continuer...')
    os.system('cls')

# verifie si le joueur ne deborde pas le nombre de jeton verticalement 
def deborde(x, y):
    return (0 <= y < 6 and 0 <= x < 7) == False

# defini le compteur de combo du joueur 
def compter_combo(pos, direction):
    y, x = pos[0], pos[1]
    dy, dx = direction[0], direction[1]
    gj = grille_jeu
    combo = 0
    for i in range(1, 4):
        ny, nx = y + i * dy, x + i * dx
        if deborde(
                nx,
                ny) or gj[ny - dy][nx - dx] != gj[ny][nx] or gj[ny][nx] == '.':
            break
        combo += 1
    return combo

# verifiez verticalement les jetons pour voir si le joueur a gagnez 
def verif_vertical(pos):
    return compter_combo(pos, [-1, 0]) >= 3

# verifiez horizontalement les jetons pour voir si le joueur a gagnez 
def verif_horizontal(pos):
    return compter_combo(pos, [0, 1]) + compter_combo(pos, [0, -1]) >= 3

# verifiez les diagonal des jetons pour voir si le joueur a gagnez 
def verif_diagonale_slash(pos):
    return compter_combo(pos, [1, 1]) + compter_combo(pos, [-1, -1]) >= 3


# verifiez les diagonal inverse  des jetons pour voir si le joueur a gagnez 
def verif_diagonale_antislash(pos):
    return compter_combo(pos, [1, -1]) + compter_combo(pos, [-1, 1]) >= 3

# si un des deux joueurs a gagné, cette fonction arrette le jeux et demande au joueur d'appuyez sur entrez pour refaire une partie
def fin_jeu(s):
    global grille_jeu
    afficher_grille()
    grille_jeu = [['.'] * 7 for _ in range(6)]
    print(s)
    input('Appuyez sur entrée pour continuer...')
    os.system('cls')

#defini les frille de 1 a 7
grille_jeu = [['.'] * 7 for _ in range(6)]
#compteur du nombre de tout 0 ou 1
tour = 0
#demande au joueur de definir un nom pour pouvoir jouer 
nom_joueurs = [input('Nom du joueur 1: '), input('Nom du joueur 2: ')]

os.system('cls')  # Clear écran
#cette boucle verifie chaque fonction 
while True:
    try:
        afficher_grille()
        n_joueur = tour % 2  # soit 0 soit 1
        couleur = ROUGE if n_joueur == 1 else BLEU
        jeton = colorier(couleur, 'O')
        colonne = demander_colonne(n_joueur, jeton)
        pos = placer_jeton(colonne, jeton)
        os.system('cls')

        fin_jeu_str = colorier(couleur,
                               f'\n"{nom_joueurs[n_joueur]}" a gagné!')
        if verif_egalite():
            fin_jeu(colorier(JAUNE, '\nÉgalité!'))
            fin_jeu_egalite()
        elif verif_vertical(pos):
            fin_jeu(fin_jeu_str)
        elif verif_horizontal(pos):
            fin_jeu(fin_jeu_str)
        elif verif_diagonale_slash(pos):
            fin_jeu(fin_jeu_str)
        elif verif_diagonale_antislash(pos):
            fin_jeu(fin_jeu_str)
        tour += 1
    except Exception as e:
        os.system('cls')
        print(colorier(JAUNE, f'{e}\n'))


