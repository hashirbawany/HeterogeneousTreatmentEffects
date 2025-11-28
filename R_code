library(dplyr)
library(stringr)
library(haven)  
library(patchwork)
library(tidyverse)
#rm(list = ls())


#Section 1: loading dataframe
df <- read_dta("C:/Users/hashi/OneDrive - Higher Education Commission/Desktop/Job work/DIME/Old intervention/Pooled paper work/end_POOL_regstats_hh.dta")



#Section 2: Cleaning the dataset 
df <- df %>%

  filter(table_name %in% c(
    "all__a1_codeq",
    "all__a1_depres",
    "all__a1_lieu_nos_rev",
    "all__a1_minmkt_rev")
      ) %>%

  
    
  filter(outcome_var == "consum_2_day_eq_ppp") %>%
  
  
  
  select(-outcome_label) %>%
  
  
  
  mutate(outcome_label = "Gross consumption (daily, USD/adult eq.)") %>%
  
  
  
  #Keeping only the point estimates we need
  filter(var_name %in% c(
    "1bn.treatment", "2.treatment", "3.treatment",
    "T1_HTE1_total", "T2_HTE1_total", "T3_HTE1_total")
        ) %>%
  
  
  
  # Generating new variables - Vulnerability Groups, Treatment arms,
  # significance dummy, heterogenous groups  
  mutate(
    
    # Creating 2 vulnerability groups - vulnerable & most vulnerable
    vulnerability_group = case_when(
      var_name %in% c("1bn.treatment", "2.treatment", "3.treatment")
        ~ "Most Deprived", TRUE  ~ "Deprived"
    ),
    
    # Creating 3 treatment arms
    treatment = case_when(
      str_detect(var_name, "1bn") | str_detect(var_name, "T1")         ~ "Capital",
      str_detect(var_name, "2\\.treatment") | str_detect(var_name, "T2") ~ "Social",
      str_detect(var_name, "3\\.treatment") | str_detect(var_name, "T3") ~ "Full"
    ),
    
    # Generating dummy for 95% significance 
    significant = if_else(ci_95_lower > 0 | ci_95_upper < 0, 1L, 0L),
    
    # Creating dimensions/heterogeneity
    dimension_label = recode(heterogeneity,
                             "med_cons2deq_dum_hte"  = "Consumption Level",
                             "med_mendep_dum_hte"    = "Mental Health",
                             "med_minmkt_dum_hte_rev"= "Market Access",
                             "dist_dum_bl_rev"       = "Geographic Access"
    ),
    
    # Encoding treatment
    treatment_num = recode(treatment,
                           "Capital" = 1L,
                           "Social"  = 2L,
                           "Full"    = 3L
    ),
    
    # Encoding dimension
    dimension_order = recode(dimension_label,
    "Consumption Level"  = 1L,
    "Mental Health"      = 2L,
    "Geographic Access"  = 3L,
    "Market Access"      = 4L
    ),
    
    # Forming a merge variable for dimension and treatment_type to help with graphs
    dim_trt = paste(dimension_label, treatment, sep = " - "),
    
    # Applying Value labels 
    dim_trt_num = case_when(
      dim_trt == "Consumption Level - Capital"     ~ 1,
      dim_trt == "Consumption Level - Social"      ~ 2,
      dim_trt == "Consumption Level - Full"        ~ 3,
      dim_trt == "Mental Health - Capital"         ~ 4,
      dim_trt == "Mental Health - Social"          ~ 5,
      dim_trt == "Mental Health - Full"            ~ 6,
      dim_trt == "Geographic Access - Capital"     ~ 7,
      dim_trt == "Geographic Access - Social"      ~ 8,
      dim_trt == "Geographic Access - Full"        ~ 9,
      dim_trt == "Market Access - Capital"         ~ 10,
      dim_trt == "Market Access - Social"          ~ 11,
      dim_trt == "Market Access - Full"            ~ 12,
      TRUE                                         ~ NA_real_  
    ),
    
  ) %>%
  
  
  
  # Removing Unnecessary Variables 
  select(-table_name, -table_title, -heterogeneity, -var_name,
         -country, -phase, -outcome_var) %>%
  
  
  
  # Sorting
  arrange(vulnerability_group, dim_trt_num , ) %>%
  
  
  
  # Ordering
  select(vulnerability_group,
         dim_trt, 
         dim_trt_num, 
         dimension_label,
         dimension_order,
         treatment,
         treatment_num,
         everything()
  )



# Splitting data into by Vulnerability
vulnerable_data      <- df %>% filter(vulnerability_group == "Deprived")
more_vulnerable_data <- df %>% filter(vulnerability_group == "Most Deprived")



# Creating a df for dimensions just for axis so dimensions arent repeated in Y axis
axis_df <- vulnerable_data %>% 
  group_by(dimension_label) %>% 
  summarise(center = mean(dim_trt_num), .groups = "drop") %>% 
  arrange(center)



# Section 3.1: Plotting
  
  #3.1.1: Plotting Most vulnerable 
  p_most <- ggplot(more_vulnerable_data,
              aes(x = pt_est, y = dim_trt_num, colour = treatment)) +
              
              geom_errorbarh(aes(xmin = ci_90_lower, xmax = ci_90_upper), height = .25) +
                    
              geom_point(aes(size = ifelse(significant == 1, 7, 3)),
                         alpha = 1) +
    #, 
    #                     show.legend = FALSE) +
               
              scale_size_identity() +
              
              geom_vline(xintercept = 0, linetype = "dashed") +               
              
              scale_y_continuous(
                breaks = axis_df$center,
                labels = axis_df$dimension_label,          
                expand = expansion(mult = c(.02, .05))
              ) +
    
              scale_colour_manual(values = c(Capital = "red",
                                 Social  = "blue",
                                 Full    = "green")) +
  
              labs(title = "Most Deprived Households",
                x = NULL, y = NULL, colour = NULL) +
  
              theme_minimal(base_size = 13) +
              
              theme(
                plot.title         = element_text(hjust = .5),
                panel.grid.major.y = element_blank(),
                axis.text.y        = element_text(size = 12)
                #legend.position    = "none"
              )

  
  #3.1.2: Plotting Vulnerable
  p_less <- ggplot(vulnerable_data,
                 aes(x = pt_est,
                     y = dim_trt_num,
                     colour = treatment)) +
              
              geom_errorbarh(aes(xmin = ci_90_lower, xmax = ci_90_upper),
                              height = 0.25) +
    
              geom_point(aes(size = ifelse(significant == 1, 7, 3)),
                          alpha = 1, ) +
                         #show.legend = FALSE) +
              
              scale_size_identity() +
              
              geom_vline(xintercept = 0, linetype = "dashed") +
              
              scale_y_continuous(
                breaks = axis_df$center,      
                labels = NULL,
                expand = expansion(mult = c(0.02, 0.05))
              ) +
              
              scale_colour_manual(values = c(
                Capital = "red",
                Social  = "blue",
                Full    = "green"
              )) +
              
              labs(
                title = "Deprived Households",
                x     = "Treatment Effect (95% CI)",
                y     = NULL,
                colour = NULL
              ) +
              
              theme_minimal(base_size = 13) +
              
              theme(
                plot.title         = element_text(hjust = 0.5),
                panel.grid.major.y = element_blank(),
                axis.text.y        = element_blank(),   
                axis.ticks.y       = element_blank()   
                #legend.position    = "none"             
              )

  #3.1.3: Stitching together
  (p_most | p_less) +
  
  plot_layout(guides = "collect") &
    theme(legend.position = "bottom")
