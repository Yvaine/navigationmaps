#pragma once
#include <graphics.h>
#include <easygl_constants.h>
#include "m1.h"
#include <utility>//for pair class in valid_feature_attribute
#include <algorithm>
#include<unordered_map>
#include<string>
using namespace std;
// Draws the map whose at map_path; this should be a .bin file.
void draw_map(std::string map_path);
void drawscreen(void);
void set_up_is_major_datastructure(void);
void act_on_mouse_press (float x, float y, t_event_buttonPressed event);
t_bound_box getWorldCoords(void);
void drawStreets(void);
//void draw_one_way_arrows(LatLon current_end_point_coordinates1, LatLon current_end_point_coordinates2);
void calculate_and_draw_path_between_two_intersections(unsigned intersection_1, unsigned intersection_2);
void drawStreetNames(void);
void check_if_on_screen_and_draw_street_names(void);
bool check_if_in_window(t_point);
void draw_poi(void);
void draw_features(void);
void draw_closed_feature(t_point *points, const unsigned attribute_point_count, unsigned feature_id);
void draw_open_feature(t_point *points, const unsigned attribute_point_count,unsigned feature_id);
void set_color(unsigned feature_id);


void feature_attributes(string);
double feature_area(unsigned feature_id);
string find_closest_intersection(LatLon curr_position);
void delay(long milliseconds);
void button_features(void drawscreen(void));
void button_streets(void drawscreen(void));
void button_PoIs(void drawscreen(void));

t_point latlon_to_xy(LatLon);
LatLon xy_to_LatLon(t_point);