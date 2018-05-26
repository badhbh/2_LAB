#include <iostream>
#include <stack>

#include <glib.h>
#include <libxml++/libxml++.h>
#include <iomanip>

struct valute
{
    Glib::ustring name, nominal, value, code;
};

class CParser : public xmlpp::SaxParser
{
private:
    valute v_from, v_to, current_info;
    std::stack<Glib::ustring> tags;

public:
    CParser(Glib::ustring& first_currency, Glib::ustring& second_currency)
    {
        v_from.code = first_currency;
        v_from.name = "Российский рубль";
        v_from.nominal = "1";
        v_from.value = "1";
        v_to.code = second_currency;
        v_to.name = "Российский рубль";
        v_to.nominal = "1";
        v_to.value = "1";
    }

    virtual ~CParser() {}

    void print_result()
    {    
        double nominal_from = std::stod(v_from.nominal);
        double nominal_to = std::stod(v_to.nominal);

        double value_from = std::stod(v_from.value.substr(0, v_from.value.find(",")) + '.'
            + v_from.value.substr(v_from.value.find(",") + 1, v_from.value.length()));
        double value_to = std::stod(v_to.value.substr(0, v_to.value.find(",")) + '.'
            + v_to.value.substr(v_to.value.find(",") + 1, v_to.value.length()));

        value_from /= nominal_from;
        value_to /= nominal_to;

        std::cout << std::fixed << std::setprecision(2) << 1 << " " << v_from.name << "(" << v_from.code << ") -> " << value_from / value_to << " " << v_to.name << "(" << v_to.code << ")" << std::endl;
        

    }

protected:
    virtual void on_start_document() override
    {

    }

    virtual void on_end_document() override
    {

    }

    virtual void on_start_element(const Glib::ustring& name,
                                  const AttributeList& properties) override
    {
        tags.push(name);

        if (name == "Valute")
        {
            current_info.name.clear();
            current_info.nominal.clear();
            current_info.value.clear();
            current_info.code.clear();
        }
    }

    virtual void on_end_element(const Glib::ustring& name) override
    {
        tags.pop();

        if (name == "Valute")
        {
            if (current_info.code == v_from.code)
            {
                v_from = current_info;
            }
            else if (current_info.code == v_to.code)
            {
                v_to = current_info;
            }
        }
    }

    virtual void on_characters(const Glib::ustring& chars) override
    {
        if (tags.top() == "CharCode")
        {
            current_info.code = chars;
        }
        else if (tags.top() == "Nominal")
        {
            current_info.nominal = chars;
        }
        else if (tags.top() == "Name")
        {
            current_info.name = chars;
        }
        else if (tags.top() == "Value")
        {
            current_info.value = chars;
        }
    }

    virtual void on_comment(const Glib::ustring& text) override
    {

    }

    virtual void on_warning(const Glib::ustring& text) override
    {

    }

    virtual void on_error(const Glib::ustring& text) override
    {

    }

    virtual void on_fatal_error(const Glib::ustring& text) override
    {

    }
};

int main(int argc, char** argv)
{
    Glib::ustring filename("data.xml");
    Glib::ustring from("RUB"), to("RUB");
    
    std::locale::global(std::locale(""));

    for (int i = 1; i < argc - 1; ++i)
    {
        Glib::ustring arg = argv[i];
        if (arg == "--file")
            filename = argv[++i];
        else if (arg == "--from")
            from = argv[++i];
        else if (arg == "--to")
            to = argv[++i];
    }
    if (from == to) {
        std::cout << "Incorrect input" << std::endl;
        return 0;
    }
    CParser parser(from, to);
    parser.parse_file(filename);
    parser.print_result();
    return 0;
}
