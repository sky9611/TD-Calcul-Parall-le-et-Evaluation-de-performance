# Mini-Aevol : A mini-application based on the Aevol simulator


## Evaluation de performance pour OpenMP

Après l'optimisation de OpenMP, on a réduit 35% de temps d'exécution(testé sur un processeur de 4 coeurs). On a constaté que l'utilisation de OpenMP sur la plupart des boucles for n'a aucun effet, quelques-unes ont même un effet négatif. C'est peut-être parce que le nombre de coeurs n'est pas suffisant pour réaliser la parallélisation. Cependant, il y a deux utilisations de #pragma qui nous fournit une optimisation considérable de temps d'exécution:

- 25-30% réduction de temps d'exécution,dans la fonction "void ExpManager::run_a_step"  
        #pragma omp parallel for  
        for (int indiv_id = 0; indiv_id < nb_indivs_; indiv_id++) {  
            do_mutation(indiv_id);  
        }  

- 5-10% réduction de temps d'exécution,dans la fonction de construction "ExpManager::ExpManager"  
        #pragma omp parallel for  
        for (int indiv_id = 0; indiv_id < nb_indivs_; indiv_id++) {  
            prev_internal_organisms_[indiv_id] = internal_organisms_[indiv_id] =  
                std::make_shared<Organism>(this, internal_organisms_[0]);  
            internal_organisms_[indiv_id]->indiv_id_ = indiv_id;  
            internal_organisms_[indiv_id]->parent_id_ = 0;  
            internal_organisms_[indiv_id]->global_id = AeTime::time()*nb_indivs_+indiv_id;  
        }  

Les autres utilisations de OpenMp n'apportent pas d'optimisation évidente. Surtout, il faut faire attention à l'utilisation de OpenMP sur les boucles compliquées, qui ralentit souvent beaucoup la vitesse.
