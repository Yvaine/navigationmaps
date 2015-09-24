#include <cstdlib>
#include <iostream>
#include <graphics.h>
#include <easygl_constants.h>
#include <math.h>
#include <string>
#include <cfloat>
#include<unordered_map>
#include "m1.h"
#include <m2.h>
#include "m3.h"
#include "StreetsDatabaseAPI.h"
#include <iomanip>  //for setw(X)
#include<unordered_set>
#include<sstream>

using namespace std;

// GLOBAL VARIABLES
float orig_area, curr_area;

    //number of clicks for different functions
int feature_button_clicks = 0;
int street_button_clicks = 0;
int poi_button_clicks = 0;
int number_of_mouse_clicks = 0;

    //other global variables
string city_name;
bool features_button;
double lat_avg;
float average_street_length;
unsigned intersection_id_for_drawpath, intersection_id2_for_drawpath;

    //Hashtable to identify different streets by length
unordered_map<unsigned, bool> major_or_minor;

void draw_map(string map_path) 
{
    load_map(map_path);
   // Extracting city name to display on the map as a special feature.
    int position_beg = map_path.find("maps/");
    int position_end = map_path.find('.');

    city_name = map_path.substr(position_beg + 5, position_end - position_beg - 5);
    char c = city_name[0];
    c = c - 32;
    city_name[0] = c;
    // For city names that are two words
    for (unsigned i = 0; i < city_name.length(); i++) 
    {

        if (city_name[i] == '_') 
        {
            city_name[i + 1] -= 32;
            city_name[i] = ' ';
        }
    }
    init_graphics("Map", t_color(224, 224, 224)); //GREY

    //This next block finds the average latitude for converting LatLons into XY.
    
        //find the minimum and maximum latitude of the area to be mapped
        LatLon min, max;
    
        min = max = getIntersectionPosition(0);
        for (unsigned i = 0; i < getNumberOfIntersections(); i++) 
        {            
            if (min.lat > getIntersectionPosition(i).lat) 
            {
                min.lat = getIntersectionPosition(i).lat;
            } 
            else if (max.lat < getIntersectionPosition(i).lat) 
            {
                max.lat = getIntersectionPosition(i).lat;
            }
        }
        lat_avg = (max.lat + min.lat)/2;
        lat_avg = lat_avg * DEG_TO_RAD;
    
    
    //set up data structure that identifies whether a street is major or minor.
    set_up_is_major_datastructure();
    
    // Setting the world coordinates
    set_visible_world(getWorldCoords());

    
    // Calculating the area of the original mapped coordinates.
    t_report * orig_coords = new t_report;
    get_report_structure(orig_coords);
    float orig_xleft = orig_coords->xleft;
    float orig_xright = orig_coords->xright;
    float orig_ytop = orig_coords->ytop;
    float orig_ybottom = orig_coords->ybot;
    orig_area = abs((abs(orig_xright) - abs(orig_xleft))*(abs(orig_ytop) - abs(orig_ybottom)));

    // Creating 3 buttons to toggle Features, Streets and PoIs.
    const char* prev_button_text = "Window";
    const char* button_text = "Features";
    create_button(prev_button_text, button_text, button_features);

    prev_button_text = "Features";
    button_text = "Streets";
    create_button(prev_button_text, button_text, button_streets);

    prev_button_text = "Streets";
    button_text = "PoIs";
    create_button(prev_button_text, button_text, button_PoIs); 
    
    event_loop(act_on_mouse_press, NULL, NULL, drawscreen);
    
    delete orig_coords;
    close_graphics();
    close_map();    
}


// Multiple special features implemented on mouse click.

void act_on_mouse_press(float x, float y, t_event_buttonPressed event) 
{
    //Special feature Implementation on right_click;
    // Make the map follow users mouse click, i.e. make the lat/lon of mouse click the center of visible world.
    t_bound_box curr_world_coords = get_visible_world();
 //   float y_cen = curr_world_coords.get_ycenter();
    //float x_cen = curr_world_coords.get_xcenter();

  //  float delta_y = y - y_cen;
 //   float delta_x = x - x_cen;

  //  curr_world_coords.offset(delta_x, delta_y);

    //set_visible_world(curr_world_coords);
  //  drawscreen();

    // Find closest intersection on mouse click
    LatLon position_clicked_latlon;     
    t_point position_clicked_xy;        
    position_clicked_xy.x = x;         
    position_clicked_xy.y = y;          
    
    //Converts position clicked from an XY to LatLon so that the closest intersection can be found.
    position_clicked_latlon = xy_to_LatLon(position_clicked_xy);
    
    string intersection_name = find_closest_intersection(position_clicked_latlon);
    
    // Mouse clicks determines how many times the mouse has been clicked.
    number_of_mouse_clicks++;
    // For an odd number of mouse clicks
    if(number_of_mouse_clicks % 2 == 1)
    {
        intersection_id_for_drawpath = find_intersection_id_from_name(intersection_name);
    }
    else
    {
        intersection_id2_for_drawpath = find_intersection_id_from_name(intersection_name);
        drawscreen();
    }
    
    //**********************************************************************************
    string closest_street_name, closest_PoI;
    double speed_limit, travel_time;

    float box_left, box_bottom, box_right, box_top, text_x, text_y;
    
    if(curr_area > 0.0000005*orig_area)
    {
        //The following block describes creating the Special Feature.
        //****************************************************************************
        //Create the block to display the closest street name and speed limit.
        box_left = curr_world_coords.bottom_left().x;
        //changes the size of the box in the x-direction
        box_right = curr_world_coords.bottom_left().x + (0.6 * curr_world_coords.get_width());
        box_bottom = curr_world_coords.bottom_left().y;
        //changes the size of the box in the y-direction
        box_top = curr_world_coords.bottom_left().y + (0.12 * curr_world_coords.get_height());
        
        //Set x and y location for text "Closest Street Name" and "Speed Limit"
        text_x = ((box_left + box_right) / 2) - 0.09 * curr_world_coords.get_width();
        text_y = box_top - (0.01 * curr_world_coords.get_height());
        
        //Draw text "Closest Street Name" and "Speed Limit"
        setcolor(t_color(255, 128, 0));
        fillrect(box_left + (0.01 * curr_world_coords.get_width()), box_bottom + (0.01 * curr_world_coords.get_width()), box_right, box_top);
        setcolor(WHITE);
        setfontsize(10);
        drawtext(text_x, text_y, "Closest Street Name", FLT_MAX, FLT_MAX);
        
        text_x = ((box_left + box_right) / 2) + 0.09 * curr_world_coords.get_width();
        drawtext(text_x, text_y, "Speed Limit (km/h)", FLT_MAX, FLT_MAX);
        
        // Filtering out duplicate street names at the intersection.    
    }
        
    vector<unsigned> closest_street_segments_id;
    closest_street_segments_id = find_intersection_street_segments(intersection_name);

    // set of unique names at the intersection
    unordered_set<string> unique_street_names;

    for (unsigned i = 0; i < closest_street_segments_id.size(); i++) 
    {
        string street_name = getStreetName(getStreetSegmentStreetID(closest_street_segments_id[i]));
        unique_street_names.insert(street_name);
    }

    unsigned location_index = 2;
    for (unordered_set<string>::iterator itr = unique_street_names.begin(); itr != unique_street_names.end(); ++itr, location_index++) 
    {
        //Displays the closest street name, speed limit and travel time in Terminal.
        unsigned street_id = find_street_id_from_name(*itr);
        speed_limit = getStreetSegmentSpeedLimit(street_id);
        travel_time = find_segment_travel_time(street_id) * 60;
        closest_street_name = *itr;

        if(curr_area > 0.0000005*orig_area)
        {
        //The following block describes the information in the Special Feature.
        //This Special Feature displays the information above on the display.
        //*********************************************************************************
        text_x = ((box_left + box_right) / 2) - 0.09 * curr_world_coords.get_width();
        text_y = box_top - ((location_index)*0.02 * curr_world_coords.get_height());

        //drawing the street name.
        setcolor(WHITE);
        setfontsize(8);
        drawtext(text_x, text_y, closest_street_name, FLT_MAX, FLT_MAX);

        text_x = ((box_left + box_right) / 2) + 0.09 * curr_world_coords.get_width();

        //converting the speed into a string, so that it can be drawn.
        string speed = to_string((int) speed_limit);
        drawtext(text_x, text_y, speed, FLT_MAX, FLT_MAX);
        
        //*********************************************************************************
        }
    }

    //cout << "********************" << endl;
    flushinput();
}

// Toggle Features, Streets and PoIs on by clicking the button on the display.
void button_features(void drawscreen(void)) 
{
    feature_button_clicks++;
    clearscreen();
    drawscreen();
}

void button_streets(void drawscreen(void)) 
{
    street_button_clicks++;
    clearscreen();
    drawscreen();
}

void button_PoIs(void drawscreen(void)) 
{
    poi_button_clicks++;
    clearscreen();
    drawscreen();
}

//Sets up the datastructure that is used later in the code to identify main/minor streets.
void set_up_is_major_datastructure(void) 
{
    average_street_length = 0;
    string street_name;
    bool major_or_minor_index;

    //Finds the average street length
    for (unsigned k = 0; k < getNumberOfStreets(); k++) 
    {
        street_name = getStreetName(k);
        average_street_length += (float) find_street_length(street_name);
    }
    average_street_length = average_street_length / getNumberOfStreets();

    // Hash Table for StreetNames, get street id by doing street_ids_from_name[string here]
    for (unsigned j = 0; j < getNumberOfStreets(); j++) 
    {
        street_name = getStreetName(j);
        if (find_street_length(street_name) > (average_street_length*sqrt(average_street_length)) && street_name != "(unknown)") 
        {
            major_or_minor_index = true;
        }
        else 
        {
            major_or_minor_index = false;
        }
        major_or_minor.insert(make_pair(j, major_or_minor_index));
    }
}
// Get min and max lat and lon of the city to draw initial map.

t_bound_box getWorldCoords(void) 
{
    LatLon min, max;
    min = max = getIntersectionPosition(0);
    for (unsigned i = 0; i < getNumberOfIntersections(); i++) 
    {

        if (min.lat > getIntersectionPosition(i).lat) 
        {
            min.lat = getIntersectionPosition(i).lat;
        } 
        else if (max.lat < getIntersectionPosition(i).lat) 
        {
            max.lat = getIntersectionPosition(i).lat;
        }

        if (min.lon > getIntersectionPosition(i).lon) 
        {
            min.lon = getIntersectionPosition(i).lon;
        } 
        else if (max.lon < getIntersectionPosition(i).lon) 
        {
            max.lon = getIntersectionPosition(i).lon;
        }

    }

    t_point min_xy = latlon_to_xy(min);
    t_point max_xy = latlon_to_xy(max);
    t_bound_box map_coords = t_bound_box(min_xy.x, min_xy.y, max_xy.x, max_xy.y);
    
    /*cout<<"LEFT: "<<map_coords.left()<<" "<<min.lat<<endl;
    cout<<"RIGHT: "<<map_coords.right()<<" "<<max.lat<<endl;
    cout<<"BOTTOM: "<<map_coords.bottom()<<" "<<min.lat<<endl;
    cout<<"TOP: "<<map_coords.top()<<" "<<max.lat<<endl;*/

    return map_coords;
}

void drawscreen(void) 
{
    set_draw_mode(DRAW_NORMAL);
    clearscreen();

    // Calculating current area, so that different items can be displayed on zoom in/zoom out.
    t_report * curr_coords = new t_report;
    get_report_structure(curr_coords);
    curr_area = abs((abs(curr_coords->xright) - abs(curr_coords->xleft))*(abs(curr_coords->ytop) - abs(curr_coords->ybot)));

    if(curr_area < 0.0000005*orig_area)
    {
        update_message("Too much zoom!!!");
    }
    else
    {    
        update_message("Interactive map of " + city_name);
    }
    
   //Only display features if toggle features button is pressed an even number of times
    if (feature_button_clicks % 2 == 0) 
    {
        draw_features();
    }

    // Only display Streets and Street Names if the toggle streets button is pressed an even number of times
    if (street_button_clicks % 2 == 0) 
    {
        drawStreets();
        if (curr_area < orig_area) 
        {
            drawStreetNames();
        }
    }
    
    if (number_of_mouse_clicks%2 == 0 && number_of_mouse_clicks != 0)
    {
        calculate_and_draw_path_between_two_intersections(intersection_id_for_drawpath, intersection_id2_for_drawpath);
    }

    //Only display PoIs if toggle poi button is pressed an even number of times
    if (poi_button_clicks % 2 == 0) 
    {
        draw_poi();
    }
    
    settextattrs(50, 0);
    setcolor(BLACK);
    setlinestyle(SOLID);
    t_bound_box curr_world_coords = get_visible_world();
    float y_cen = curr_world_coords.get_ycenter();
    float x_cen = curr_world_coords.get_xcenter();

    // Displaying city name on initial map.
    if (curr_area == orig_area) 
    {
        drawtext(x_cen, y_cen, city_name, 100, 150);
    }

    delete curr_coords;
}

void drawStreets(void) 
{
    setfontsize(10);
    setlinestyle(SOLID);
    setlinewidth(6);
    setcolor(WHITE);

    StreetSegmentEnds current_end_point_ends;
    LatLon current_end_point_coordinates1, current_end_point_coordinates2;
    unsigned street_segment_id, average_speed_limit = 0;
    unsigned number_of_street_segments = getNumberOfStreetSegments();

    for(street_segment_id = 0; street_segment_id < number_of_street_segments; street_segment_id++)
    {
        average_speed_limit += getStreetSegmentSpeedLimit(street_segment_id);
    }
    
    average_speed_limit = average_speed_limit/number_of_street_segments;
    street_segment_id = 0;
    while (street_segment_id < number_of_street_segments) 
    {
        bool is_one_way = false;
        unsigned street_id = getStreetSegmentStreetID(street_segment_id);
        string street_name = getStreetName(street_id);
        //Filters what to draw depending on:
        //Total Area (draw everything), if a street is major or minor (draws that street only) or if above a certain speed limit - for connectivity
         if ((curr_area < 0.012 * orig_area || major_or_minor[street_id] == true || getStreetSegmentSpeedLimit(street_segment_id) > average_speed_limit*2) && street_name != "unknown")
         {
            //unsigned street_id = getStreetSegmentStreetID(street_segment_id);
            unsigned curve_point_count = getStreetSegmentCurvePointCount(street_segment_id);

            //Select line size depending on the level of zoom.
            if (curr_area < 0.00003 * orig_area) 
            {
                setlinewidth(7);
            } 
            else if (curr_area < 0.0006 * orig_area) 
            {
                setlinewidth(5);
            } 
            else if (curr_area < 0.0012 * orig_area) 
            {
                setlinewidth(3);
            } 
            else 
            {
                setlinewidth(2);
            }
            //selecting major streets from  minor streets.
            if (getStreetSegmentSpeedLimit(street_segment_id) > 80) 
            {
                setcolor(ORANGE);
            } 
            //selecting if it's one way or not.
            else if (getStreetSegmentOneWay(street_segment_id) == true) 
            {
                //One way
                setcolor(YELLOW);
                is_one_way = true;
            } 
            else 
            {
                setcolor(WHITE);
            }

                current_end_point_ends = getStreetSegmentEnds(street_segment_id);
                current_end_point_coordinates1 = getIntersectionPosition(current_end_point_ends.from);
                current_end_point_coordinates2 = getIntersectionPosition(current_end_point_ends.to);
                
                t_point end_point_coords1_xy = latlon_to_xy(current_end_point_coordinates1);
                t_point end_point_coords2_xy = latlon_to_xy(current_end_point_coordinates2);
                
                //Only draw the street IF it's on the screen.
                if(check_if_in_window(end_point_coords1_xy) || check_if_in_window(end_point_coords2_xy))
                {
                    if (curve_point_count == 0) 
                    {
                        t_point end_point_coords1_xy = latlon_to_xy(current_end_point_coordinates1);
                        t_point end_point_coords2_xy = latlon_to_xy(current_end_point_coordinates2);
                        drawline(end_point_coords1_xy.x, end_point_coords1_xy.y, end_point_coords2_xy.x, end_point_coords2_xy.y);
                    } 
                    else 
                    {
                        unsigned curve_point_index;
                        LatLon curve_point, prev_curve_point;
                        prev_curve_point.lat = current_end_point_coordinates1.lat;
                        prev_curve_point.lon = current_end_point_coordinates1.lon;
                        
                        for (curve_point_index = 0; curve_point_index < curve_point_count; curve_point_index++) 
                        {
                            curve_point = getStreetSegmentCurvePoint(street_segment_id, curve_point_index);
                            t_point curve_point_xy = latlon_to_xy(curve_point);
                            t_point prev_curve_point_xy = latlon_to_xy(prev_curve_point);
                            drawline(prev_curve_point_xy.x, prev_curve_point_xy.y, curve_point_xy.x, curve_point_xy.y);
                            prev_curve_point = curve_point;
                        }
                        
                        t_point curve_point_xy = latlon_to_xy(curve_point);
                        t_point end_point_coords2_xy = latlon_to_xy(current_end_point_coordinates2);
                        
                        //if(is_one_way == true)
                        //{
                        //    draw_one_way_arrows(current_end_point_coordinates1, current_end_point_coordinates2);
                        //}
                        drawline(curve_point_xy.x, curve_point_xy.y, end_point_coords2_xy.x, end_point_coords2_xy.y);
                    }   
                }
            }
        street_segment_id++;
    }
}

void calculate_and_draw_path_between_two_intersections(unsigned intersection_1, unsigned intersection_2)
{
    /**********************************************************************************/
    //The following code DRAWS THE POINTS between two intersections (MILESTONE 3)
    StreetSegmentEnds street_seg_ends;
    unsigned street_segment_curve_point_count;
    auto path = find_path_between_intersections(intersection_1, intersection_2);
    if(path.size() == 0)
    {
        return;
    }
    for(unsigned i = 0; i < path.size(); i++)
    {
        setcolor(RED);
        
        street_seg_ends = getStreetSegmentEnds(path[i]);
        street_segment_curve_point_count = getStreetSegmentCurvePointCount(path[i]);
        t_point intersection1 = latlon_to_xy(getIntersectionPosition(street_seg_ends.from));
        t_point intersection2 = latlon_to_xy(getIntersectionPosition(street_seg_ends.to));        
        
        if(street_segment_curve_point_count == 0)
        {
            drawline(intersection1.x, intersection1.y, intersection2.x, intersection2.y);
        }
        else
        {
            t_point prev_curve_point;
            prev_curve_point.x = intersection1.x;
            prev_curve_point.y = intersection1.y;
            
            for(unsigned j = 0; j < street_segment_curve_point_count; j++)
            {
                t_point curve_point_xy = latlon_to_xy(getStreetSegmentCurvePoint(path[i],j));
                drawline(prev_curve_point.x, prev_curve_point.y, curve_point_xy.x, curve_point_xy.y);
                
                prev_curve_point.x = curve_point_xy.x;
                prev_curve_point.y = curve_point_xy.y;
            }
            drawline(prev_curve_point.x, prev_curve_point.y, intersection2.x, intersection2.y);            
        }
    }
    cout<<"Travel Time: " <<compute_path_travel_time(path)<<endl;
    /***********************************************************************************/
}

void drawStreetNames(void) 
{
    setfontsize(9);
    setcolor(BLACK);
    //unsigned street_segment_id = 0;
    LatLon current_end_point_coordinates1, current_end_point_coordinates2;
    StreetSegmentEnds current_end_point_ends;
    float side_opposite, side_adjacent, angle;

    //check_if_on_screen_and_draw_street_names();
    //Loops through all the streets and draws the street (doesn't have to check if it's already been written)
    unsigned street_id;
    unsigned long long number_of_streets = getNumberOfStreets();
    LatLon lat_lon_centre;

    //If in a certain zoom range, call "check_if_on_screen_and_draw_street_names()" function, that
    //draws all the streets in the visible world.
    if (curr_area < 0.0000008 * orig_area) 
    {
        setfontsize(7);
        check_if_on_screen_and_draw_street_names();
        return;
    }

    //Loop through all the streets and draw their street names ONCE.
    for (street_id = 0; street_id < number_of_streets; street_id++) 
    {
        string street_name = getStreetName(street_id);
        settextrotation(0);
        LatLon draw_text_lat_lon;
        
        //Vector that gets all the street segments so that we can write the name of the street only ONCE on ONE street segment.
        std::vector<unsigned> all_street_segments = find_street_street_segments(street_name);

        //DRAW control statement, that only draws the streets if they're major, or if at a certain zoom level.
        if ((major_or_minor[street_id] == true || curr_area < 0.0006 * orig_area) && (street_name != "(unknown)") && (all_street_segments.size() != 0))
        {
            //Change the font size if zoomed in a lot.
            if (curr_area < 0.0006 * orig_area) 
            {
                setfontsize(8);
            }
            
            //Get middle street segment, so that the name is drawn on the middle segment.
            unsigned num_of_street_segments = getSize(street_name);
            int middle_segment = floor((num_of_street_segments + 1) / 2);

            unsigned street_segment_id = all_street_segments[middle_segment - 1]; //middle_segment-1
            //float street_seg_length = find_street_segment_length(street_segment_id);
            unsigned street_segment_curve_point_count = getStreetSegmentCurvePointCount(street_segment_id);
            //float side_opposite, side_adjacent, angle;

            current_end_point_ends = getStreetSegmentEnds(street_segment_id);
            current_end_point_coordinates1 = getIntersectionPosition(current_end_point_ends.from);
            current_end_point_coordinates2 = getIntersectionPosition(current_end_point_ends.to);
                
            LatLon first_curve_point;

            //Calculates the angle the text should be written.
            //First convert them into XY coords from LatLon.
                t_point end_point_coords_xy1 = latlon_to_xy(current_end_point_coordinates1);
                t_point end_point_coords_xy2 = latlon_to_xy(current_end_point_coordinates2);
                    
                side_opposite = ((float) end_point_coords_xy2.y - (float) end_point_coords_xy1.y);
                side_adjacent = ((float) end_point_coords_xy2.x - (float) end_point_coords_xy1.x);
                angle = (atan((double) side_opposite / (double) side_adjacent)) / DEG_TO_RAD;
                                    
            //Finds the centre of the street segment/curve points.
            if (street_segment_curve_point_count != 0) 
            {
                //For multiple street curve points, only draws the name on the FIRST curve point.
                //... where "current_end_point_coordinates2" is the middle curve point.
                unsigned middle_curve_point = floor((street_segment_curve_point_count+1)/2);
                current_end_point_coordinates2 = getStreetSegmentCurvePoint(street_segment_id, middle_curve_point);
                lat_lon_centre.lon = (float) abs(current_end_point_coordinates2.lon - current_end_point_coordinates1.lon) / 2;
                lat_lon_centre.lat = (float) abs(current_end_point_coordinates2.lat - current_end_point_coordinates1.lat) / 2;
            }
            else 
            {
                lat_lon_centre.lon = (float) abs(current_end_point_coordinates2.lon - current_end_point_coordinates1.lon) / 2;
                lat_lon_centre.lat = (float) abs(current_end_point_coordinates2.lat - current_end_point_coordinates1.lat) / 2;
            }

            //Calculates the location of where the text should be drawn, by:
            // current_end_point_coordinates1 + centre of the segment(calculated above)
            if (current_end_point_coordinates1.lat < current_end_point_coordinates2.lat) 
            {
                draw_text_lat_lon.lat = current_end_point_coordinates1.lat + lat_lon_centre.lat;
            }
            else 
            {
                draw_text_lat_lon.lat = current_end_point_coordinates1.lat - lat_lon_centre.lat;
            }
            if (current_end_point_coordinates1.lon < current_end_point_coordinates2.lon) 
            {
                draw_text_lat_lon.lon = current_end_point_coordinates1.lon + lat_lon_centre.lon;
            }
            else 
            {
                draw_text_lat_lon.lon = current_end_point_coordinates1.lon - lat_lon_centre.lon;
            }
            
            t_point draw_text_xy = latlon_to_xy(draw_text_lat_lon);
            if (check_if_in_window(draw_text_xy) == true) 
            {
                    //settextrotation((float) angle);
                    drawtext(draw_text_xy, street_name, FLT_MAX, FLT_MAX);
            }            
        }
    }
}

//Function that draws the street names around an intersection.
//Only called when close to a street, and only draws the name if in the visible screen.

void check_if_on_screen_and_draw_street_names(void) 
{
    LatLon current_end_point_coordinates1, current_end_point_coordinates2;
    StreetSegmentEnds current_end_point_ends;
    LatLon draw_text_lat_lon;

    for (unsigned i = 0; i < getNumberOfIntersections(); i++) 
    {
        LatLon intersection_position = getIntersectionPosition(i);
        unsigned intersection_street_segment_count = getIntersectionStreetSegmentCount(i);

        t_point intersection_position_xy = latlon_to_xy(intersection_position);
        //"check_if_in_window (position)" = function that checks if LatLon "position" is in the window.
        //If the "intersection_position" is in the window, then draw all four street segments connected to the intersection.
        if (check_if_in_window(intersection_position_xy) == true) 
        {
            for (unsigned j = 0; j < intersection_street_segment_count; j++) 
            {
                float lat_centre, lon_centre;
                unsigned intersection_street_segment_id = getIntersectionStreetSegment(i, j);
                unsigned street_id = getStreetSegmentStreetID(intersection_street_segment_id);
                string street_name = getStreetName(street_id);

                //Invalid street name.
                if (street_name != "(unknown)")
                {
                    //Get the street segment ends on an intersection.
                    current_end_point_ends = getStreetSegmentEnds(intersection_street_segment_id);
                    current_end_point_coordinates1 = getIntersectionPosition(current_end_point_ends.from);
                    current_end_point_coordinates2 = getIntersectionPosition(current_end_point_ends.to);
                    float side_opposite, side_adjacent, angle;
                    float street_seg_length = find_street_segment_length(intersection_street_segment_id);
                    
                    //Calculates the location of the middle of the street.
                    
                        lon_centre = (float) abs(current_end_point_coordinates2.lon - current_end_point_coordinates1.lon) / 2;
                        lat_centre = (float) abs(current_end_point_coordinates2.lat - current_end_point_coordinates1.lat) / 2;
                    
                    //Calculates the angle the text should be written.
                    //First convert them into XY coords from LatLon.
                        t_point end_point_coords_xy1 = latlon_to_xy(current_end_point_coordinates1);
                        t_point end_point_coords_xy2 = latlon_to_xy(current_end_point_coordinates2);
                        
                        side_opposite = ((float) end_point_coords_xy2.y - (float) end_point_coords_xy1.y);
                        side_adjacent = ((float) end_point_coords_xy2.x - (float) end_point_coords_xy1.x);
                        angle = (atan((double) side_opposite / (double) side_adjacent)) / DEG_TO_RAD;
                    
                    //Calculates the position in which the text should be written.
                    if (current_end_point_coordinates1.lat < current_end_point_coordinates2.lat) 
                    {
                        draw_text_lat_lon.lat = current_end_point_coordinates1.lat + lat_centre;
                    } 
                    else 
                    {
                        draw_text_lat_lon.lat = current_end_point_coordinates1.lat - lat_centre;
                    }
                    
                    if (current_end_point_coordinates1.lon < current_end_point_coordinates2.lon) 
                    {
                        draw_text_lat_lon.lon = current_end_point_coordinates1.lon + lon_centre;
                    } 
                    else 
                    {
                        draw_text_lat_lon.lon = current_end_point_coordinates1.lon - lon_centre;
                    }
                    
                    //convert drawing coords from LatLon to XY.
                    t_point draw_text_xy = latlon_to_xy(draw_text_lat_lon);
                    if (check_if_in_window(draw_text_xy) == true) 
                    {
                        settextrotation((float) angle);
                        drawtext(draw_text_xy.x, draw_text_xy.y, street_name, street_seg_length, street_seg_length);
                    }
                }
            }
        }   
    }
}

//function that checks if LatLon position is in the visible world.

bool check_if_in_window(t_point position) 
{
    t_bound_box current_visible_world = get_visible_world();
    if (position.x > current_visible_world.left() && position.x < current_visible_world.right() && position.y > current_visible_world.bottom() && position.y < current_visible_world.top()) 
    {
        return true;
    }
    else 
    {
        return false;
    }
}

//Drawing point of interests on the map. Represented with blue dots
void draw_poi(void) 
{
    setcolor(BLUE);
    settextrotation(0); 
    unsigned total_poi = getNumberOfPointsOfInterest();
    for (unsigned poi_id = 0; poi_id < total_poi; poi_id++) 
    {
        LatLon one_point = getPointOfInterestPosition(poi_id);
        t_point center, text;
        center = latlon_to_xy(one_point);
        // Move text a little below the circle
        text.x = center.x;
        text.y = center.y - 0.00000025;

        if (check_if_in_window(center) == true) 
        {
            // Draw different sized circles based on zoom
            if (curr_area > 0.00001 * orig_area && curr_area < 0.12 * orig_area) 
            {
                fillarc((float) center.x, (float) center.y, (float) 0.00000025, 0, 360);
            } 
            else if (curr_area < 0.00001 * orig_area) 
            {
                setcolor(BLUE);
                fillarc((float) center.x, (float) center.y, (float) 0.0000001, 0, 360);
                setcolor(BLACK);
                if(curr_area < 0.0000008 * orig_area)
                {
                    setfontsize(8);
                }
                else
                {
                    setfontsize(7);
                }
                drawtext(text, getPointOfInterestName(poi_id), 100, 150);
            }
        }
    }

}

void draw_features(void) 
{
    unsigned total_features = getFeatureCount();
    for (unsigned feature_id = 0; feature_id < total_features; feature_id++) 
    {
        const unsigned attribute_point_count = getFeaturePointCount(feature_id);
        t_point points[attribute_point_count];
        LatLon one_point;

        for (unsigned j = 0; j < attribute_point_count; j++) 
        {
            // Convert lat and lon to cartesian coordinates
            one_point = getFeaturePoint(feature_id, j);
            points[j] = latlon_to_xy(one_point);
        }
        
        //Identifies whether the feature structure is open(e.g. rivers/streams) or closed (e.g. parks).
        if (points[0].x == points[attribute_point_count - 1].x && points[0].y == points[attribute_point_count - 1].y) 
        {
            draw_closed_feature(points, attribute_point_count, feature_id);
        }
        else 
        {
            draw_open_feature(points, attribute_point_count, feature_id);
        }
    }
}

void draw_closed_feature(t_point *points, const unsigned attribute_point_count, unsigned feature_id) 
{
    bool draw_poly = false;
    setlinestyle(SOLID);
    setlinewidth(1);
    // set color based on what feature it is.
    set_color(feature_id);

    for(unsigned i = 0; i < attribute_point_count; i++)
    {
        if(check_if_in_window(points[i]) == true)
        {
            draw_poly = true;
        }
    }
    
    if(draw_poly == true)
    {
        fillpoly(points, attribute_point_count);
    }

}

// Draw open features
void draw_open_feature(t_point *points, const unsigned attribute_point_count, unsigned feature_id) 
{
    setlinestyle(SOLID);
    setlinewidth(1);
    // set color based on what feature it is.
    set_color(feature_id);

    for (unsigned i = 0; i < attribute_point_count - 1; i++) 
    {
        if(check_if_in_window(points[i]) == true)
        {
            drawline(points[i].x, points[i].y, points[i + 1].x, points[i + 1].y);
        }
    }
}
// Set color of different features based on their colors observed in nature.

void set_color(unsigned feature_id) 
{
    //use this website for consistency:
    //https://web.njit.edu/~kevin/rgb.text.html
    string water_body = getFeatureAttribute(feature_id, "water");
    string natural = getFeatureAttribute(feature_id, "natural");
    string waterway = getFeatureAttribute(feature_id, "waterway");
    string landuse = getFeatureAttribute(feature_id, "landuse");
    string leisure = getFeatureAttribute(feature_id, "leisure");
    if (water_body != "") 
    {
        setcolor(t_color(135, 206, 235)); //"TURQUOISE"
        //setcolor(t_color(135, 206, 235)); //"TURQUOISE"
    }
    if (natural != "") 
    {
        setcolor(t_color(255, 235, 205)); //"SANDY ORANGE"
    }

    if (waterway != "") 
    {
        setcolor(t_color(135, 206, 235)); //"CADE BLUE2"
    }

    if (landuse != "") 
    {
        setcolor(t_color(135, 206, 235)); //"CADE BLUE2"
    }

    if (leisure != "") 
    {
        setcolor(t_color(173, 255, 47)); //"GREEN YELLOW"
    }

    //Colour everything else in LIGHTGREY.
    if (natural == "" && water_body == "" && waterway == "" && landuse == ""
            && leisure == "") 
    {
        setcolor(t_color(224, 224, 224)); 
    }

}

std::string find_closest_intersection(LatLon curr_position) 
{
    double intersection_distance = EARTH_RADIUS_IN_METERS; //Initialize to an impossible distance
    LatLon location_of_intersection;
    unsigned closest_intersection_id = 0;

    // Loop through all the intersections, and find the intersection with the shortest distance to curr_position.
    for (unsigned intersection_id = 0; intersection_id < getNumberOfIntersections(); intersection_id++) 
    {
       location_of_intersection = getIntersectionPosition(intersection_id);
       double temp_distance = find_distance_between_two_points(curr_position, location_of_intersection);

        //Store intersection_id that is currently the closest
        if (temp_distance < intersection_distance) 
        {
            intersection_distance = temp_distance;
            closest_intersection_id = intersection_id;
        }
    }
    
    return getIntersectionName(closest_intersection_id);
}

t_point latlon_to_xy(LatLon position)
{           
    //convert deg to rads because the cosine function only below accepts radians        
    double position_lon = position.lon * DEG_TO_RAD;
    double position_lat = position.lat * DEG_TO_RAD;
    
    //this is going to be the final calculated x-y value from the lat lon.
    t_point final_xy;
    
    //taken from m1 handout (pg. 9)
    final_xy.x = position_lon * cos(lat_avg);
    final_xy.y = position_lat;
    
    return final_xy;
}

LatLon xy_to_LatLon(t_point position)
{
    //this is going to be the final calculated x-y value from the lat lon.
    LatLon final_latlon;
    
    //taken from m1 handout (pg. 9)
    final_latlon.lon = position.x/cos(lat_avg);
    final_latlon.lat = position.y;
    
    final_latlon.lon = final_latlon.lon/DEG_TO_RAD;
    final_latlon.lat = final_latlon.lat/DEG_TO_RAD;
    
    return final_latlon;
 }